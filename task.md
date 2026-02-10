# Task Tracker: MongoDB-RAG-Heroku

## Phase A: Code Migration
- [x] A1. Detect tech stack and framework
- [x] A2. Create CLAUDE.md with full audit
- [x] A3. Create app_platform branch
- [x] A4. Audit and upgrade packages
- [x] A5. Translate Heroku → DO (code changes)
- [x] A6. Create/update Dockerfile
- [x] A7. Create .env.docker for local testing
- [x] A8. Create .env.remote for deployment
- [x] A9. Build Docker image
- [x] A10. Run container and test locally (medium: discover routes)
- [x] A11. Commit and push to app_platform branch

**PHASE_A: COMPLETE**

## Phase B: Deploy to App Platform
- [ ] B1. Read CLAUDE.md for context
- [ ] B2. Create DB/user in shared cluster (if needed)
- [ ] B3. Update .env.remote with DB connection strings
- [ ] B4. Push secrets to GitHub
- [ ] B5. Create GitHub Actions deploy workflow
- [ ] B6. Create/validate .do/app.yaml
- [ ] B7. Deploy to App Platform
- [ ] B8. Verify deployment (status, logs, endpoints)
- [ ] B9. Update CLAUDE.md with deployment details
- [ ] B10. Final verification

**PHASE_B: PENDING**

## Blockers
- LangChain v1.x import path compatibility issues prevent Docker build
- Module not found errors for @langchain/community/vectorstores/mongodb_atlas
- All LangChain imports need updating to new package structure
- Phase B needs to resolve these before deployment
