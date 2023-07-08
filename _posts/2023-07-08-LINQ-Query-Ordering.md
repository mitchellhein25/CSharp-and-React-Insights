---
title: "LINQ Query Ordering"
date: 2023-07-08
---

## Ordering

In a recent article titled [LINQ: Select.Where or Where.Select?](https://steven-giesel.com/blogPost/57ed9867-4afd-4d02-9f35-e0941bc6f715?utm_source=csharpdigest&utm_medium&utm_campaign=1674), I was reminded of the importance of being mindful of how we chain our LINQ statements. It's easy to become complacent and not consider the order in which we write them. However, as was emphasized in the article, the order can have a significant impact on the performance of our queries.

Most of the time, we don't pay much attention to the order of our LINQ queries. We tend to focus more on the desired outcome rather than the sequence of operations. However, it's crucial to prioritize the filtering functions first. By doing so, we ensure that subsequent methods are executed on a smaller set of values, which can greatly improve performance.

## Performance

It's worth noting that while LINQ provides a convenient and readable way to handle data manipulation, it's not always the most efficient option. In fact, LINQ queries can be around 10% slower than writing custom algorithms for specific operations. Nevertheless, we often choose to sacrifice that slight performance hit in favor of code maintainability and simplicity.

For those interested in delving deeper into LINQ performance, I came across a helpful stack overflow thread titled [LINQ performance FAQ](https://stackoverflow.com/questions/4044400/linq-performance-faq). It provides valuable insights and considerations when working with LINQ.

## Conclusion

Within our LINQ statements, it's crucial to take an extra second to ensure that our LINQ calls make sense and are as efficient as possible. While LINQ offers convenience, it's important to remember that a little extra care in our coding practices can go a long way in optimizing performance.

By being mindful of the order in which we chain LINQ statements and considering alternative approaches when necessary, we can strike a balance between efficiency and maintainability in our code.
