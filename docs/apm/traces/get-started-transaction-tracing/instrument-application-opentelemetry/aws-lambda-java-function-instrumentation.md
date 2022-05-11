---
id: aws-lambda-java-function-instrumentation
---

# AWS Lambda - Java function instrumentation with Sumo Logic tracing

This document covers how to install and configure OpenTelemetry distributed tracing for AWS Lambda functions based on Java and send the data to Sumo Logic.

## Requirements

It is very simple to instrument your AWS Java Lambda function using the [Sumo Logic AWS distro Lambda layer](https://github.com/SumoLogic/opentelemetry-lambda/tree/main/java). By default calls to the Lambda function and AWS Services are instrumented, see the [Manual Instrumentation](#optional-manual-instrumentation) section below if your function is performing some other calls like HTTP requests or database calls.

You'll need the following:

* Java8 (Corretto) or Java11 (Corretto)
* Lambda layers add permissions
* HTTP Traces Source endpoint URL - To send spans from the instrumented Lambda function to Sumo Logic you need an endpoint URL from an existing or new [HTTP Traces Source](../http-traces-source.md).

Sumo Logic AWS Distro Lambda Layer supports:

* Java8 (Corretto) and Java11 (Corretto)
* x86_64 and arm64 architectures

## Sumo Logic AWS Distro Lambda layer 

1. Navigate to [functions](https://console.aws.amazon.com/lambda/home#/functions) in the AWS Lambda Console and open the function you want to instrument.

1. Navigate to the **Layers** section and click **Add a layer**.

1. In the **Choose a layer** menu, select **Specify an ARN** and paste the ARN ID for your Lambda function AWS Region. Reference the [amd64](#amd64-architecture) and [arm64](#arm64-architecture) tables for the ARN ID.  

    ![lambda-java1.png](/img/traces/lambda-java1.png)

1. Ensure the AWS Distro layer is present in the Layers section:

    ![lambda-java2.png](/img/traces/lambda-java2.png)

1. Navigate to the **Configuration \> Environment variables** section and set up the following **required** environment variables:

   * `AWS_LAMBDA_EXEC_WRAPPER` environment variable configures the appropriate wrapper for a specific type of lambda handler function. Set the value appropriate for your handler:
   
     * `/opt/otel-handler` - if implementing RequestHandler
     * `/opt/otel-proxy-handler` - if implementing RequestHandler but proxied through API Gateway
     * `/opt/otel-stream-handler` - if implementing RequestStreamHandler

   * `OTEL_SERVICE_NAME = YOUR_SERVICE_NAME` - Ensure you define it as a string value that represents the function name and its business logic such as "Check SQS Lambda". This will appear as the tracing service name in Sumo Logic.
   * Tracing `application` and `cloud.account.id` are set with the `OTEL_RESOURCE_ATTRIBUTES` environment  variable.

     * `application=YOUR_APPLICATION_NAME` - the string value, if the function is a part of complex system/application then set it for all other functions/applications.

     * `cloud.account.id=YOUR_CLOUD_ACCOUNT_ID` - set an additional tag that will contain your [AWS Lambda Account ID](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html). This will help to provide more relevant data.   
              
        All of the attributes above are comma separated key/value pairs (this is also a way to add additional information to the spans, just after comma add additional key=value pair) such as, `OTEL_RESOURCE_ATTRIBUTES=application=YOUR_APPLICATION_NAME,cloud.account.id=123456789012`.

     * `SUMOLOGIC_HTTP_TRACES_ENDPOINT_URL` has to be set to send all gathered telemetry data to Sumo Logic. The URL comes from an [HTTP Traces Endpoint URL](../http-traces-source.md). You can use an existing Source or create a new one if needed.  
    
    ![img](/img/traces/lambda-java3.png)

1. Your function should be successfully instrumented. Invoke the function and find your traces in the [Sumo Logic Tracing screen](../../working-with-tracing-data/view-and-investigate-traces.md).

## Optional manual instrumentation

By default, only calls to the Lambda function and AWS Services are instrumented to decrease instrumentation overhead. If the Lambda function is performing some other calls like HTTP requests or database calls it's worth providing additional instrumentation to get a better understanding of the Lambda execution.

See the [OpenTelemetry Java Instrumentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation/tree/main/instrumentation) repository and check if they are on the list of supported packages. Some changes in the code are needed and the specific package will have to be added to the Lambda function dependencies. The steps below show how to instrument calls from the [OkHttp](https://github.com/open-telemetry/opentelemetry-java-instrumentation/tree/main/instrumentation/okhttp/okhttp-3.0/library)
library.

1. Add the OkHttp instrumentation package to your Lambda function dependencies.

1. Update the imports list, add:

    ```
    import io.opentelemetry.instrumentation.okhttp.v3_0.OkHttpTracing;
    import io.opentelemetry.api.GlobalOpenTelemetry;
    ```

1. Initialize tracing for the OkHttp library.

    ```
    okHttpClient client =
                new OkHttpClient.Builder()
    .addInterceptor(OkHttpTracing.create(GlobalOpenTelemetry.get()).newInterceptor())
        .build();
    ```

This will generate all the spans related to the calls made by the OkHttp
library.

## Context propagation

In case of external request to the Lambda function, it is important to propagate the context. Enabling [AWS X-Ray context propagation](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-tracingheader) on the client side will help to visualize the complex flow of the trace. For applications instrumented by OpenTelemetry SDK it is enough to install AWS X-Ray propagator dependency specific for an
instrumentation and configure [`OTEL_PROPAGATORS` environment variable](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/sdk-environment-variables.md#general-sdk-configuration)
(for example: `export OTEL_PROPAGATORS= tracecontext,baggage,xray`).

## amd64 architecture

The following are Sumo Logic AWS Distro Lambda layers for AWS Region amd64 (x86_64) architecture.

| AWS Region | ARN |
|--|--|
| US East (N.Virginia) us-east-1 | arn:aws:lambda:us-east-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| US East (Ohio) us-east-2 | arn:aws:lambda:us-east-2:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| US West (N.Carolina) us-west-1 | arn:aws:lambda:us-west-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| US West (Oregon) us-west-2 | arn:aws:lambda:us-west-2:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| Africa (Cape Town) af-south-1 | arn:aws:lambda:af-south-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1     |
| Asia Pacific (Hong Kong) ap-east-1 | arn:aws:lambda:ap-east-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| Asia Pacific (Mumbai) ap-south-1 | arn:aws:lambda:ap-south-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1     |
| Asia Pacific (Osaka) ap-northeast-3 | arn:aws:lambda:ap-northeast-3:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| Asia Pacific (Seoul) ap-northeast-2 | arn:aws:lambda:ap-northeast-2:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| Asia Pacific (Singapore) ap-southeast-1 | arn:aws:lambda:ap-southeast-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| Asia Pacific (Sydney) ap-southeast-2 | arn:aws:lambda:ap-southeast-2:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| Asia Pacific (Tokyo) ap-northeast-1 | arn:aws:lambda:ap-northeast-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1 |
| Canada (Central) ca-central-1 | arn:aws:lambda:ca-central-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1   |
| Europe (Frankfurt) eu-central-1 | arn:aws:lambda:eu-central-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1   |
| Europe (Ireland) eu-west-1 | arn:aws:lambda:eu-west-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| Europe (London) eu-west-2 | arn:aws:lambda:eu-west-2:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| Europe (Milan) eu-south-1 | arn:aws:lambda:eu-south-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1     |
| Europe (Paris) eu-west-3 | arn:aws:lambda:eu-west-3:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |
| Europe (Stockholm) eu-north-1 | arn:aws:lambda:eu-north-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1     |
| Middle East (Bahrain) me-south-1 | arn:aws:lambda:me-south-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1     |
| South America (Sao Paulo) sa-east-1 | arn:aws:lambda:sa-east-1:663229565520:layer:sumologic-otel-java-x86_64-ver-1-12-1:1      |

## arm64 architecture

The following are Sumo Logic AWS Distro Lambda layers for AWS Region arm64 architecture.

| AWS Region | ARN |
|--|--|
| US East (N.Virginia) us-east-1          | arn:aws:lambda:us-east-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1      |
| US East (Ohio) us-east-2                | arn:aws:lambda:us-east-2:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1      |
| US West (Oregon) us-west-2              | arn:aws:lambda:us-west-2:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1      |
| Asia Pacific (Mumbai) ap-south-1        | arn:aws:lambda:ap-south-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1     |
| Asia Pacific (Osaka) ap-northeast-3     | arn:aws:lambda:ap-northeast-3:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1 |
| Asia Pacific (Singapore) ap-southeast-1 | arn:aws:lambda:ap-southeast-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1 |
| Asia Pacific (Sydney) ap-southeast-2    | arn:aws:lambda:ap-southeast-2:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1 |
| Asia Pacific (Tokyo) ap-northeast-1     | arn:aws:lambda:ap-northeast-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1 |
| Europe (Frankfurt) eu-central-1         | arn:aws:lambda:eu-central-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1   |
| Europe (Ireland) eu-west-1              | arn:aws:lambda:eu-west-1:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1      |
| Europe (London) eu-west-2               | arn:aws:lambda:eu-west-2:663229565520:layer:sumologic-otel-java-arm64-ver-1-12-1:1      |