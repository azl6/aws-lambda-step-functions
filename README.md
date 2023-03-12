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

**Concurrency**

![image](https://user-images.githubusercontent.com/80921933/224443556-8e6db83d-1c00-48dd-bf2e-e61d6ea33142.png)

# Concurrency types

![image](https://user-images.githubusercontent.com/80921933/224436101-0c384332-4b21-4437-ad1b-9389236979a2.png)

# Unreserved concurrency

This is the default behaviour of Lambda. By default, we can have 1000 in-flight requests per account per region. It means that the 1001st request will be throttled.

# Reserved concurrency

This is when we reserve concurrency for a given function. 

Suppose we have **Function A** and reserve 100 requests for it. That means, the rest of the functions will have 900 concurrent requests available, and **Function A** will have a hard-limit of 100 (the 101st request will be throttled)

![image](https://user-images.githubusercontent.com/80921933/224440801-6ea6d571-04ab-4385-98cc-ec8792a5da76.png)

# Provisioned concurrency

Used to have conteiners always on to run your function and therefore bypass cold-start.

![image](https://user-images.githubusercontent.com/80921933/224441528-8c933dc3-4405-4523-8a2c-0f9e4a90d757.png)

**Provisioned concurrency "depends" on the existence of reserved concurrency.**

![image](https://user-images.githubusercontent.com/80921933/224441848-e886a351-7f4d-45d0-a3e7-b4f1f7915e6a.png)

# Lambda versions and aliases

We can have different versions of the same Lambda function:

![image](https://user-images.githubusercontent.com/80921933/224557915-0e8b58af-0f77-405d-839a-6202165f90e3.png)

We can also alias our versions with a certain string, such as **prod**:

![image](https://user-images.githubusercontent.com/80921933/224558048-ffb3e62c-28c5-4628-b00f-68f727a3927c.png)

Aliases can be used to either associate this string with a version, or to balance a % of traffic to two versions simultaneously (e.g the image above, that show 80% of traffic going to V1 and 20% going to V2)

# Environment variables

We can provide environment variables to our Lambda functions.

```python
import json
import os

def lambda_handler(event, context):
    myEnv = os.environ["AWS_REGION"] # Python's way of grabbing env variables
    print(myEnv)
```

`AWS_REGION` is a pre-built environment variable. We can provide our own by going to **Configuration -> Environment Variables** and adding our desired environment variables.

# Important Lambda metrics

![image](https://user-images.githubusercontent.com/80921933/224559737-fe1697ca-97a7-43ec-ba35-ab49c717db1e.png)

![image](https://user-images.githubusercontent.com/80921933/224559753-fecd7eb2-8ba7-4b3a-8cfe-8fc932fd1eeb.png)

