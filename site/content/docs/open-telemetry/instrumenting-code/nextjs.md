---
title: Next.js
head:
  title: "Instrumenting Next.js with OpenTelemetry"
metatags:
  title: "Instrumenting Next.js with OpenTelemetry"
  description: "Instrument your Next.js application with OpenTelemetry and send traces to Checkly."
weight: 37
menu:
  integrations:
    parent: "Instrumenting your code with OpenTelemetry"
beta: true
---

Next.js is now shipping with an experimental OpenTelemetry integration. This guide will help you instrument your Next.js 
application(s) with OpenTelemetry and send traces to Checkly.
<!--more-->
## Step 1: Install the `@vercel/otel` package

For Next.js, Vercel has created a wrapper that should get you going very quickly. We're just adding some extra packages
so we can filter out the Checkly traces.

```bash
npm install --save \
  @vercel/otel \
  @opentelemetry/api \
  @opentelemetry/sdk-trace-base \
  @opentelemetry/exporter-trace-otlp-http    
```

{{< warning >}}
Make sure to install v1.9.1 or later of the `@vercel/otel` package as it contains a critical bug fix for Vercel's Edge runtime.
{{< /warning >}}


## Step 2: Initialize the instrumentation

Set the `instrumentationHook` flag to `true` in your Next.js configuration file. This will enable the OpenTelemetry instrumentation.

```js
/** @type {import('next').NextConfig} */
const nextConfig = { 
  experimental: { 
    instrumentationHook: true 
  }
}

module.exports = nextConfig
```

Create a file called `instrumentation.ts` at the root of your project and add the following code:

```ts
// instrumentation.ts
import { registerOTel } from '@vercel/otel'
import { SamplingDecision } from '@opentelemetry/sdk-trace-base'
import { trace, Context } from '@opentelemetry/api'

export function register() {
  registerOTel({
    serviceName: 'acme-next-app',
    traceExporter: 'auto',
    spanProcessors: ['auto'],
    traceSampler: {
      shouldSample: (context: Context) => {
        const isChecklySpan = trace.getSpan(context)?.spanContext()?.traceState?.get('checkly')
        if (isChecklySpan) {
          console.log('Sampling decision for Checkly span:', SamplingDecision.RECORD_AND_SAMPLED)
          return {
            decision: SamplingDecision.RECORD_AND_SAMPLED
          }
        } else {
          console.log('Sampling decision for non-Checkly span:', SamplingDecision.NOT_RECORD)
          return {
            decision: SamplingDecision.NOT_RECORD
          }
        }
      },
    },
  })
```

Notice that we are using the default exporter and span processor from the `@vercel/otel` package, which is configured by using the `auto` value.
This package is compatible with Vercel's Edge runtime, in contrast to the `@opentelemetry/exporter-trace-otlp-http` package, which is not.

If you are not using Vercel or don't use Vercel's Edge runtime, you can also use the `@opentelemetry/exporter-trace-otlp-http` 
and just follow [the regular Node.js instrumentation guide](/docs/open-telemetry/instrumenting-code/nodejs/#step-2-initialize-the-instrumentation).
This is however not required, the above configuration should work just fine on Node.js and Edge runtimes.

Also notice the `sampler` configuration. This is a custom, **head-based sampler** that will only sample spans that are generated by Checkly by
inspecting the trace state. This way you only pay for the egress traffic generated by Checkly and not for any other traffic.

## Step 3: Start your app with the instrumentation

{{< markdownpartial "/_shared/otel-api-and-endpoint.md" >}}

If you are using Vercel for hosting your Next.js app, add the above environment variables to your Vercel project settings,
e.g. 👇

![Vercel project OTEL variables](/docs/images/integrations/otel/otel-languages/otel_vercel_env_vars.png)


## Further reading

Have a look at the official Next.js docs on [how to enable the experimental OpenTelemetry integration](https://nextjs.org/docs/app/building-your-application/optimizing/open-telemetry).