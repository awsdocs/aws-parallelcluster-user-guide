# Step 2: Test the API in the Amazon API Gateway console<a name="tutorials_06_multi-API-use-step2"></a>

1. Sign in to the AWS Management Console

1. Navigate to the [Amazon API Gateway console](https://console.aws.amazon.com/apigateway/home)\.

1. Choose your API deployment\.  
![\[Amazon API Gateway console with list of your gateways you can choose from.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/gateway_choose.png)

1. Choose **Stages** and select a stage\.  
![\[A console view of the stages you can choose from. You can also view the URL that API Gateway provides for your API.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/gateway_address.png)

1. Note the URL that API Gateway provides for accessing or invoking your API\. It's highlighted in blue\.

1. Choose **Resources**, and select `GET` under `/clusters`\.

1. Choose the **TEST** icon and then scroll down and choose **TEST** icon\.  
![\[A console view of the API resources and test mechanisms.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/gateway_test.png)

   The response to your `/clusters GET` appears\.  
![\[A console view of the API resources, test mechanisms, and the response from your test request.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/gateway.png)