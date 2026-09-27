# Lambda Versioning and Aliases

## Lambda Versions

**Versions** are **immutable snapshots** of your Lambda function — code plus configuration (memory, timeout, environment variables, layers).

- When you publish a version, a unique **version number** is assigned (V1, V2, V3...)
- **$LATEST** is the mutable, unpublished version — always represents your most recent code changes
- After publishing, a version is **frozen** — you cannot modify its code or configuration
- Each version gets its own ARN: `arn:aws:lambda:region:account:function:MyFunction:3`

**Why use versions?**
- Stable, reproducible deployments — know exactly what code ran for each version
- Enables safe rollback to a previous version
- Required for traffic shifting with aliases

## Lambda Aliases

**Aliases** are named pointers to specific Lambda versions. They act like symbolic links.

- Example aliases: `dev`, `staging`, `prod`, `v1`
- Aliases have their own ARN: `arn:aws:lambda:region:account:function:MyFunction:prod`
- When you update the alias to point to a new version, all callers using the alias ARN are updated automatically — **no client-side changes needed**
- IAM policies, triggers (API Gateway, EventBridge), and permissions can be attached to **aliases**

**Alias benefits:**
- Environment isolation: `dev` alias → V3 (development), `prod` alias → V2 (stable production)
- Rollback is instant — update the alias to point to previous version
- Callers always use the alias ARN — they never need to know the version number

## Traffic Shifting (Canary Releases)

Aliases support **weighted routing** between two versions — useful for canary deployments.

**Example:**
- `prod` alias: 80% → V3 (stable), 20% → V4 (new release)
- Gradually shift from 80/20 to 100/0 as confidence grows
- If V4 has issues, immediately set `prod` to 100% → V3

**Key rule**: An alias can split traffic between **at most 2 versions**. The secondary version cannot be `$LATEST`.

## Best Practices

- **Never use `$LATEST` in production** — it's mutable and creates unpredictable behavior
- Always publish a version before promoting to production
- Use aliases for all deployment environments (dev, staging, prod)
- Use weighted aliases for canary releases

## Versioning Flow Example

```
1. Develop code → save to $LATEST
2. Test with $LATEST
3. Publish version → V3 created (immutable snapshot)
4. Update 'prod' alias: 90% → V2 (current stable), 10% → V3 (canary)
5. Monitor V3 behavior. If healthy:
6. Update 'prod' alias: 100% → V3
7. Old V2 retained (can roll back instantly)
```

## Key Points / Exam Tips

- **Versions** are immutable — once published, code and config cannot change
- **$LATEST** is always the mutable working version — never use in production
- **Aliases** are mutable pointers — they can be updated to point to any version
- **Traffic shifting** on aliases enables canary deployments without client changes
- Each version and alias has a **unique ARN** — triggers and policies reference the alias ARN
- Deleting a version does not affect aliases pointing to it (but the alias breaks) — plan version retention carefully

## Trigger Words

- "Canary deployment / gradual rollout for Lambda" → Alias with weighted traffic split
- "Rollback Lambda to previous version instantly" → Update alias to point to previous version
- "Isolate dev, staging, prod Lambda environments" → Lambda Aliases
- "Immutable Lambda deployment artifact" → Lambda Version
- "Lambda $LATEST" → mutable, unpublished, do not use in production
