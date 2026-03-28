---
id: aws-cdk
aliases: []
tags: []
---

# AWS CDK Test Environment Reference

AWS CDK (Cloud Development Kit) for provisioning test infrastructure with serverless architecture.

## CDK Commands

```bash
# Synthesize CloudFormation template
cdk synth

# Deploy to AWS
cdk deploy

# Watch mode (auto-deploy on changes)
cdk watch

# Destroy test environment
cdk destroy

# List stacks
cdk list

# Diff against deployed stack
cdk diff

# Bootstrap environment (first time)
cdk bootstrap
```

## CDK Project Structure

```
infra/
├── cdk.json                    # CDK configuration
├── bin/
│   └── infra.ts              # Stack entry point
├── lib/
│   ├── test-stack.ts         # Test environment stack
│   └── shared-stack.ts       # Shared resources
├── lambdas/                   # Lambda functions
│   ├── handler.ts
│   └── layers/
├── apis/                      # API definitions
│   └── api-stack.ts
└── cdk.context.json          # Context values
```

## Serverless Architecture (Cost-Optimized)

### Primary Services for Test Environments

| Service | Use Case | Cost Optimization |
|---------|----------|------------------|
| **Lambda** | Compute | Pay-per-invocation, free tier 1M/month |
| **API Gateway** | HTTP endpoints | Pay-per-request, $1/million after free tier |
| **DynamoDB** | NoSQL database | On-demand mode for test, ~$1.25/million writes |
| **S3** | Static assets | $0.023/GB, minimal for test |
| **CloudFront** | CDN | Test with minimal caching |
| **EventBridge** | Event bus | $1/million events, negligible for test |
| **SQS** | Message queue | $0.40/million messages |
| **Cognito** | Auth | Free tier 50K MAU for test |

### Lambda Best Practices

```typescript
// Memory vs CPU tradeoff - 1792MB is optimal price/performance
const lambdaProps: FunctionProps = {
  runtime: Runtime.NODEJS_18_X,
  memorySize: 1792,
  timeout: Duration.seconds(30),
  layers: [sharedLayer], // Reuse common dependencies
};

// EnableprovisionedConcurrency for consistent test performance
}
```

### API Gateway for Serverless

```typescript
import { LambdaRestApi } from 'aws-cdk-lib/aws-apigateway';

const api = new LambdaRestApi(this, 'TestApi', {
  handler: lambda,
  proxy: true,
});

// For testing: throttle limits
api.root.addMethod('ANY', new LambdaIntegration(lambda), {
  throttle: {
    rateLimit: 100,
    burstLimit: 50,
  },
});
```

## Test Environment Setup Workflow

### 1. Create Test Stack

```typescript
// lib/test-stack.ts
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as apigateway from 'aws-cdk-lib/aws-apigateway';

export class TestStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Lambda function
    const handler = new lambda.Function(this, 'TestHandler', {
      runtime: lambda.Runtime.NODEJS_18_X,
      handler: 'lambdas/handler.main',
      code: lambda.Code.fromAsset('dist'),
      memorySize: 1792,
    });

    // API Gateway
    const api = new apigateway.LambdaRestApi(this, 'TestApi', {
      handler,
    });

    // Output API URL
    new cdk.CfnOutput(this, 'ApiUrl', {
      value: api.url,
    });
  }
}
```

### 2. Deploy Commands

```bash
# Build TypeScript
npm run build

# Synthesize (preview)
cdk synth

# Deploy
cdk deploy --require-approval never

# Get API URL
cdk outputs
```

### 3. Environment Variables

```typescript
// Pass environment-specific config
const api = new LambdaRestApi(this, 'TestApi', {
  handler,
  defaultIntegration: new LambdaIntegration(handler, {
    proxy: false,
  }),
});

// Add environment variables
handler.addEnvironment('STAGE', 'test');
handler.addEnvironment('LOG_LEVEL', 'debug');
```

## Cost Optimization Strategies

1. **Use Lambda Power Tuning** - Find optimal memory settings
2. **Provisioned Concurrency** - Only for performance-critical tests
3. **On-Demand DynamoDB** - Pay per request instead of capacity
4. **S3 Intelligent Tiering** - Auto-optimize storage costs
5. **CloudWatch Logs Retention** - Set 7-day retention for test
6. **Delete resources after tests** - Use `cdk destroy`

## CDK Context for Test

```json
{
  "test": {
    "region": "us-east-1",
    "account": "123456789012",
    "apiUrl": "https://xxx.execute-api.us-east-1.amazonaws.com/prod/"
  }
}
```

## Lambda Layer for Shared Dependencies

```typescript
// lib/shared-layer.ts
import * as cdk from 'aws-cdk-lib';
import * as lambda from 'aws-cdk-lib/aws-lambda';

export class SharedLayer extends cdk.Stack {
  public readonly layer: lambda.LayerVersion;

  constructor(scope: cdk.Construct, id: string) {
    super(scope, id);

    this.layer = new lambda.LayerVersion(this, 'SharedLayer', {
      code: lambda.Code.fromAsset('layers/shared'),
      compatibleRuntimes: [lambda.Runtime.NODEJS_18_X],
      description: 'Shared dependencies for test lambdas',
    });
  }
}
```
