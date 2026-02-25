# securityreview-kit-cursor

securityreview-kit plugin for cursor that generates threat models as features are defined, translates them into security requirements, and ensures controls are part of the plan and not a post-build fix.

Your vibe code now passes all vibe checks with the power of on-the-go threat modelling 


## Configuration

Set these environment variables in the environment where Cursor runs MCP servers:

- `SECURITY_REVIEW_API_URL`
- `SECURITY_REVIEW_API_TOKEN`

Optional: replace `<SRAI_PROJECT_NAME>` placeholders in rule/command files with your real project name.
