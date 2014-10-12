---
layout: post
title: Rewriting parts of an Expression Tree in F#
---

## Background

I am in the fortunate situation of working on a project where we make use of F# scripts to administer a production application. It's a very compelling approach as it allows maintenance tasks to be carried out through domain objects, rather than modifying repositories directly. This ensures that when data maintenance is performed, all appropriate business rules are applied and data integrity is maintained.

For its persistence mechanism the application makes use of LINQ to SQL. Within the scripts we build adhoc queries using [F# quotations](http://msdn.microsoft.com/en-us/library/dd233212.aspx) that are subsequently transtated into LINQ expressions to query our repository. For example we make use of the [F# power pack's](http://fsharppowerpack.codeplex.com/) ToLinqExpression in the function below to convert a quotation into a consumable LINQ expression:

{% highlight FSharp %}
let toLinq (exp : Expr<'a -> 'b>) =
    let linq = exp.ToLinqExpression()
    let call = linq :?> MethodCallExpression
    let lambda = call.Arguments.[0] :?> LambdaExpression
    Expression.Lambda<Func<'a, 'b>>(lambda.Body, lambda.Parameters)
{% endhighlight %}

Recently when I upgraded the scripts to make use of the latest version of the F# power pack I found that previously working queries now caused our application to throw exceptions that LINQ to SQL had no supported translation to SQL. My [investigation](http://stackoverflow.com/questions/6434762/f-power-pack-linq-issue) found that the cause of these errors were that the latest version of ToLinqExpression had been modified such that when a quotation made use of =, >, >=, <, <= or <> the created BinayExpression make use of a different method for its comparison. So for example if I have a binding like:

{% highlight FSharp %}
let example = toLinq (<@fun u -> u.FirstName = "Bob"@> : Expr<Account->bool>)
{% endhighlight %}

When converted to a LINQ expression the implementing method for the binary operation u.FirstName = "Bob" is set to use [GenericEqualityIntrinsic](http://msdn.microsoft.com/en-us/library/ee806482.aspx) whereas in the previous version it was set to use [String.op_Equality.](http://msdn.microsoft.com/en-us/library/system.string.op_equality(v=VS.100).aspx) As a result the LINQ to SQL provider could no longer translate the expression tree into SQL.

## Rewriting the expression tree

Having identified the issue, I decided to come up with a function to rewrite the problematic parts of the returned expression tree. Below is what I came up with.

{% highlight FSharp %}
let rec reWriteExpression  (exp:Expression) =
 
    let makeBinary e left right = Expression.MakeBinary(e, left, right) :> Expression 
 
    let modifyMethod (exp:BinaryExpression) = 
        match exp.NodeType with
        | ExpressionType.Equal as eq -> makeBinary eq exp.Left exp.Right
        | ExpressionType.GreaterThan as gt -> makeBinary gt exp.Left exp.Right
        | ExpressionType.GreaterThanOrEqual as gte -> makeBinary gte exp.Left exp.Right
        | ExpressionType.LessThan as lt -> makeBinary lt exp.Left exp.Right
        | ExpressionType.LessThanOrEqual as lte -> makeBinary lte exp.Left exp.Right
        | ExpressionType.NotEqual as ne -> makeBinary ne exp.Left exp.Right
        | _ -> makeBinary exp.NodeType (reWriteExpression exp.Left) (reWriteExpression exp.Right)
 
    match exp with
    | :? LambdaExpression -> 
        let l = exp :?> LambdaExpression
        Expression.Lambda(l.Type, reWriteExpression l.Body, l.Parameters) :> Expression
    | :? BinaryExpression -> modifyMethod (exp :?> BinaryExpression)
    | _ -> exp
{% endhighlight %}

For me, the ease by which I could come up with this rewrite highlighted two very powerful features of functional programming, namely pattern matching and recursion. [Pattern matching](http://msdn.microsoft.com/en-us/library/dd233242.aspx) is simply awesome. The simple syntax in modifyMethod to test for specific expression types makes expressing the program's intent both clear and succinct. You can do all sorts of things with pattern matching, however, I won't cover those details in this post. Similarly, routines on trees are often most easily expressed using recursion. This is natural because the tree is a recursive data type.


