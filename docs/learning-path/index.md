# Learning path

Work through the stages in order. At each stage, build a small feature, test
its failure cases, and write down the invariant it protects.

## Stage 1: Ruby

Learn objects, methods, collections, errors, blocks, testing, and the
conventions that make Ruby code readable. You should be able to model a
business rule without Rails.

## Stage 2: Web and data foundations

Learn HTTP, DNS, TCP, processes, JSON, SQL, PostgreSQL, indexes, transactions,
and security. You should be able to trace a request from a client to durable
storage.

## Stage 3: Rails application development

Learn routing, controllers, models, validation, serialization,
authentication, authorization, jobs, and GraphQL. You should be able to build
well-tested APIs with explicit error behavior and a stable schema.

## Stage 4: Architecture

Learn API contracts, queues, events, idempotency, consistency, and failure
isolation. You should be able to explain what happens when a dependency is
slow, duplicated, or unavailable.

## Stage 5: Production

Learn caching, profiling, deployment, observability, reliability, backups, and
incident response. You should be able to operate the service you built.

## Stage 6: Algorithms

Learn complexity, data structures, pagination, rate limiting, and scheduling.
You should be able to justify an algorithm using its invariant and cost.

## Expert track

After completing the stages, repeat them at a larger scale:

1. Read the framework and database documentation for the feature you use.
2. Rebuild a small version without the framework abstraction.
3. Measure it under load and identify its bottleneck.
4. Introduce a failure, observe the symptoms, and recover.
5. Write a design note comparing two valid approaches.

Expertise is demonstrated by explaining trade-offs and operating the result,
not by memorizing APIs. For each project, keep an architecture decision
record, a threat model, a load result, and a postmortem.

Use the [project sequence](projects.md) to turn each stage into practice.

!!! tip
    Do not skip testing and database fundamentals. Most Rails production bugs
    are boundary bugs: a race, an ambiguous contract, an unsafe retry, or an
    unobserved dependency.

## Exit criteria

You are ready to move on when you can:

- explain the request and data flow without opening the source code;
- write a focused test for the normal case and the failure case;
- identify which invariant belongs in Ruby, Rails, or the database;
- measure the slow boundary instead of guessing;
- describe a safe rollback or repair procedure.
