# Practices that I want to do

1. Throw a CW alarm on the number of concurrent executions and throttling of a lambda function

# Pro tips

**Maximum duration and timeouts**

![image](https://user-images.githubusercontent.com/80921933/224430758-9bf1b56c-47a1-469e-bf15-88176d389184.png)

**Memory increase**

![image](https://user-images.githubusercontent.com/80921933/224430677-ef072590-6297-4aa0-aab5-a91fdb58e7d4.png)

**Pricing and optimization**

Can also use the **Lambda Power Tuner** tool instead of the **Compute Optimizer**

![image](https://user-images.githubusercontent.com/80921933/224431776-0df38a65-09f5-47f4-8cd5-ef0c05f59435.png)

**Minimizing cold start**

![image](https://user-images.githubusercontent.com/80921933/224434036-ef3f2243-8080-4417-befd-fa725129c2ae.png)

# Concurrency types

![image](https://user-images.githubusercontent.com/80921933/224436101-0c384332-4b21-4437-ad1b-9389236979a2.png)

# Unreserved concurrency

This is the default behaviour of Lambda. By default, we can have 1000 in-flight requests per account per region. It means that the 1001st request will be throttled.

# Reserved concurrency

This is when we reserve concurrency for a given function. 

Suppose we have **Function A** and reserve 100 requests for it. That means, the rest of the functions will have 900 concurrent requests available, and **Function A** will have a hard-limit of 100 (the 101st request will be throttled)

![image](https://user-images.githubusercontent.com/80921933/224440801-6ea6d571-04ab-4385-98cc-ec8792a5da76.png)


