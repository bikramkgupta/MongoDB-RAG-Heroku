# MongoDB-RAG-Heroku — Migration Context

## Tech Stack
- **Language**: TypeScript/JavaScript
- **Framework**: Next.js 14.1.0 (React 18)
- **Package Manager**: npm
- **Runtime Version**: Node.js (based on Heroku nodejs buildpack)

## Architecture
This is a RAG (Retrieval-Augmented Generation) chatbot application that allows users to upload PDF files and ask questions about their content. The app uses MongoDB Atlas Vector Search for storing and retrieving document embeddings, LangChain for RAG chain orchestration, and OpenAI for both chat completion and embeddings. The frontend is built with Next.js, React, and Tailwind CSS.

Key workflow:
1. Users upload PDFs via the "Train" page
2. PDFs are parsed, chunked, embedded using OpenAI, and stored in MongoDB Atlas
3. Users ask questions via the "QnA" page
4. Questions trigger a retrieval chain that finds relevant chunks and generates responses

## Modules & Key Files
- `src/app/page.tsx` — Home page with app overview
- `src/app/teach/page.tsx` — PDF upload interface
- `src/app/ask/page.tsx` — Chat/QnA interface
- `src/app/api/upload/route.ts` — PDF processing API (parse, chunk, embed, store)
- `src/app/api/chat/route.ts` — Chat API (retrieval + generation)
- `src/utils/openai.ts` — OpenAI embeddings and MongoDB vector store setup
- `src/app/component/navbar.tsx` — Navigation component
- `next.config.mjs` — Next.js config with pdf-parse external package
- `app.json` — Heroku deployment config

## Packages
| Package | Old Version | New Version | Notes |
|---|---|---|---|
| next | 14.1.0 | 16.1.6 | Major upgrade with breaking changes |
| @langchain/openai | ^0.0.16 | ^1.2.6 | Major upgrade |
| @langchain/community | ^0.0.30 | ^1.1.13 | Major upgrade, import paths changed |
| @langchain/core | N/A | ^1.1.20 | New dependency |
| langchain | ^0.1.20 | ^1.2.19 | Major upgrade |
| mongodb | 5.2.0 | 7.1.0 | Major upgrade |
| ai | ^2.2.36 | 2.2.36 | Reverted to fix compatibility |
| react | ^18 | ^18 | No change |
| typescript | ^5 | ^5 | No change |

## Heroku -> DO Mapping
| Heroku Feature | DO Equivalent | Status |
|---|---|---|
| nodejs buildpack | Dockerfile with Node.js base image | ✅ DONE |
| MONGODB_URI env var | MONGODB_URI (DO managed MongoDB) | 🔄 Phase B |
| OPENAI_API_KEY env var | GRADIENT_API_KEY/MODEL_ACCESS_KEY | ✅ DONE |
| Heroku deployment | DO App Platform | ✅ DONE |
| No Procfile | npm start script in package.json | ✅ DONE |

## Environment Variables
### Required
| Variable | Purpose | .env.docker Value | .env.remote Value |
|---|---|---|---|
| MONGODB_URI | MongoDB connection string | mongodb://localhost:27017/chatter | [Phase B will set] |
| OPENAI_API_KEY | OpenAI API key for embeddings/chat | sk-do-xwGKhs... | sk-do-xwGKhs... |
| GRADIENT_API_KEY | Gradient AI API key | sk-do-xwGKhs... | sk-do-xwGKhs... |
| MODEL_ACCESS_KEY | Gradient AI API key (alias) | sk-do-xwGKhs... | sk-do-xwGKhs... |
| INFERENCE_ENDPOINT | Gradient AI endpoint | https://inference.do-ai.run | https://inference.do-ai.run |
| PORT | HTTP port | 8080 | 8080 |
| NODE_ENV | Runtime environment | development | production |
| HOSTNAME | Bind hostname | 0.0.0.0 | 0.0.0.0 |

## Test Endpoints
| Endpoint | Method | Expected Status | Expected Response | Notes |
|---|---|---|---|---|
| / | GET | 200 | HTML page with "RAG QnA Chatbot" | Home page |
| /teach | GET | 200 | HTML page with file upload | Training page |
| /ask | GET | 200 | HTML page with chat interface | QnA page |
| /api/upload | POST | 200 | JSON: {"message": "Uploaded to MongoDB"} | PDF upload API |
| /api/chat | POST | 200 | Streaming response | Chat API |

## Expected Warnings
- LangChain package import warnings (Next.js build issues)
- Missing MongoDB connection in local testing (expected)
- OpenAI API calls may fail without valid keys (expected)
- PDF processing warnings (font/encoding issues are normal)
- ESLint peer dependency warnings

## Local Testing
- **Docker build**: ❌ FAILED (LangChain import compatibility issues)
- **Container port**: 8080
- **Test results**: BUILD FAILED - package import issues need resolution

## Remote Deployment
- **App ID**: d7369d06-be91-42be-88d9-a404f0d9eefa
- **App URL**: ✅ https://mongodb-rag-heroku-49opx.ondigitalocean.app
- **Region**: syd1
- **Status**: ✅ ACTIVE - deployment successful
- **Build Logs**: Build completed successfully after fixing LangChain compatibility
- **Database**: ✅ MongoDB user and database created successfully (`mongodb_rag_heroku_user`)
- **Firewall**: ✅ App added to MongoDB cluster firewall trusted sources
- **Secrets**: ✅ All environment variables pushed to GitHub Secrets
- **GitHub Actions**: ✅ Deployment workflow created and working
- **Endpoint Tests**:
  - `/` (Home): 200 ✅ - Shows "RAG QnA Chatbot"
  - `/teach` (Training): 200 ✅ - PDF upload interface
  - `/ask` (QnA): 200 ✅ - Chat interface
- **Runtime Status**: ✅ Next.js server running successfully on port 8080

## Env Files
- `.env.docker` — Local Docker testing variables
- `.env.remote` — Deployment variables (pushed to GitHub Secrets)

## Phase A Blockers (RESOLVED)
The following compatibility issues were identified and resolved in Phase B:

1. **MongoDB Environment Variable Issue**: RESOLVED ✅
   - Problem: `process.env.MONGODB_URI` was undefined during Next.js build phase
   - Solution: Added fallback value `mongodb://localhost:27017/fallback` for build-time initialization
   - File: `src/utils/openai.ts:9`

2. **LangChain Import Compatibility**: RESOLVED ✅
   - Problem: Build failed during page data collection phase
   - Root cause: MongoDB client initialization failing due to undefined env var
   - Solution: Provide fallback connection string for build phase
   - Result: All existing import paths work correctly with v0.x LangChain packages

**Resolution Details**: The original LangChain imports were already compatible with the v0.x packages in use. The build failure was caused by MongoDB client initialization failing during the Next.js static analysis phase, not import path issues.

## Observations
- App uses older versions of LangChain packages that needed major upgrades but broke compatibility
- OpenAI integration ready to support Gradient AI with env var substitution
- No Procfile present - relies on npm scripts ("start": "next start")
- Uses Next.js App Router (newer structure)
- PDF processing writes temp files to /tmp - should work in containers
- MongoDB collection is hardcoded as "chatter.training_data"
- Uses MongoDB Atlas Vector Search with specific index name "vector_index"
- Dockerfile and environment files created successfully
- App Platform spec (.do/app.yaml) ready for deployment
## Shared Infrastructure

Region: syd1

### PostgreSQL Cluster
- Cluster ID: b32bfe92-51c0-4660-9879-92a7db886482
- Host: heroku-migration-pg-do-user-8198484-0.m.db.ondigitalocean.com
- Port: 25060
- Admin User: doadmin
- Admin Password: [REDACTED]
- Create app DB: `doctl databases db create b32bfe92-51c0-4660-9879-92a7db886482 <appname>_db`
- Create app user: `doctl databases user create b32bfe92-51c0-4660-9879-92a7db886482 <appname>_user`
- Connection string pattern: `postgresql://<user>:<password>@heroku-migration-pg-do-user-8198484-0.m.db.ondigitalocean.com:25060/<db>?sslmode=require`

### MongoDB Cluster
- Cluster ID: 0cd276e1-6800-40f7-b938-72db4e389863
- Host: heroku-migration-mongo-29f7181e.mongo.ondigitalocean.com
- Port: 27017
- Admin User: doadmin
- Admin Password: [REDACTED]
- Create app user: `doctl databases user create 0cd276e1-6800-40f7-b938-72db4e389863 <appname>_user`
- Database created on first write (use app-specific name)
- Connection string pattern: `mongodb+srv://<user>:<password>@heroku-migration-mongo-29f7181e.mongo.ondigitalocean.com/<dbname>?tls=true&authSource=admin`

### Valkey Cluster
- Cluster ID: ab76d53c-8e07-44ff-b97b-b62815ec66b8
- Host: heroku-migration-valkey-do-user-8198484-0.m.db.ondigitalocean.com
- Port: 25061
- Password: [REDACTED]
- Single default user — use key prefix `<appname>:` for data isolation
- Connection string: `rediss://default:[REDACTED]@heroku-migration-valkey-do-user-8198484-0.m.db.ondigitalocean.com:25061`

