---
title: Verifying Pacts
---

## Verify your pacts against a locally running instance of the provider

Pact is designed to give you confidence that your integration is working correctly *before* you deploy either application. To achieve this, the verification step must be run against a locally running instance of your provider on a development machine or in CI/CD. External dependencies (ie. other services) should be stubbed. If you can run a local, lightweight instance of your database, it is fine to use your real database. If that is impractical, you may be better off stubbing your data layer.

Verifying pacts against an already deployed provider will mean you don't get the benefits that contract testing was intended to provide - fast feedback, easy debugging, reliable tests. It will create a bottleneck as you won't be able to run tests in parallel, and you won't be able to use features like the webhook that trigger builds for [different versions of your provider](/pact_broker/webhooks#using-webhooks-with-the-contract_requiring_verification_published-event).

## Ensure that Pact verify runs as part of your CI build

It should run with all your other tests.

## Only stub layers beneath where contents of the request body are extracted

If you don't have to stub anything in the `Provider` when running `pact:verify`, then don't.

If you do need to stub something \(eg. a downstream system\), make sure that you only stub the code that gets executed after the contents of the request body have been extracted and validated. Otherwise, you could send any old garbage in a `POST` or `PUT` body, and no test would fail.

See [this gist](https://gist.github.com/bethesque/43eef1bf47afea4445c8b8bdebf28df0) for some conceptual background.

## Stub calls to downstream systems

Consider making a separate `Pact` with the downstream system and using shared fixtures.

See [this gist](https://gist.github.com/bethesque/43eef1bf47afea4445c8b8bdebf28df0) for some conceptual background.

### Publish verification results to the Pact Broker

As of version 2.0+ of the Pact Broker, and 1.11.1+ of the Pact Ruby implementation, provider verification results can be [published](https://github.com/pact-foundation/pact_broker/wiki/Provider-verification-results) back to the broker, and will be displayed on the index page. The consumer team should consult the verification status on the index page before deploying.

One catch - it is only safe to deploy the consumer if it was verified against the _production_ version of the provider.

## Use `can-i-deploy`

Use the [can-i-deploy](/pact_broker/can_i_deploy/) feature of the [Pact Broker CLI](/pact_broker/client_cli). It will give you a definitive answer if the version of your provider that is being deployed, is compatible with all of its known consumers.

