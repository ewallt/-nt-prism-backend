# NT Prism Backend

Express.js proxy server that provides CORS-enabled access to the Anthropic Claude API for the NT Exegetical Prism application.

## Purpose

The Claude API blocks direct browser requests due to CORS restrictions. This lightweight backend acts as a secure proxy, allowing the NT Prism frontend to leverage Claude's advanced language models for biblical text analysis while keeping API credentials server-side.

## Features

- Single endpoint proxy to Claude API
- CORS-enabled for browser requests
- Environment variable configuration for API keys
- Health check endpoint for monitoring
- Minimal dependencies (~50 lines of code)

## Tech Stack

- **Runtime**: Node.js 18+
- **Framework**: Express.js
- **Dependencies**: `cors`, `node-fetch`

## API Endpoints

### `POST /api/analyze`
Proxies analysis requests to Claude API.

**Request Body:**
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 4000,
  "messages": [
    {
      "role": "user",
      "content": "Your prompt here"
    }
  ]
}
```

**Response:**
Returns Claude API response directly.

### `GET /health`
Health check endpoint.

**Response:**
```json
{
  "status": "ok",
  "timestamp": "2025-01-18T12:34:56.789Z"
}
```

## Local Development

### Prerequisites
- Node.js 18 or higher
- Claude API key from https://console.anthropic.com/

### Setup

1. Install dependencies:
```bash
npm install
```

2. Create `.env` file:
```bash
CLAUDE_API_KEY=sk-ant-your-key-here
PORT=3000
```

3. Start server:
```bash
npm start
```

4. Test health check:
```bash
curl http://localhost:3000/health
```

5. Test with your frontend pointing to `http://localhost:3000`

## Deployment to Render

### Step 1: Prepare Repository
Push this code to a GitHub repository.

### Step 2: Create Render Service
1. Go to https://render.com/
2. Click **"New +"** → **"Web Service"**
3. Connect your GitHub account and select this repository
4. Configure:
   - **Name**: `nt-prism-backend` (or your choice)
   - **Environment**: Node
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Plan**: Free

### Step 3: Configure Environment Variables
In the Render dashboard:
1. Navigate to **"Environment"** tab
2. Add variable:
   - **Key**: `CLAUDE_API_KEY`
   - **Value**: Your Claude API key (starts with `sk-ant-`)
3. Click **"Save Changes"**

### Step 4: Deploy
- Render will automatically build and deploy
- First deployment takes ~2-3 minutes
- Your backend will be available at: `https://your-app-name.onrender.com`

### Step 5: Verify Deployment
```bash
curl https://your-app-name.onrender.com/health
```

Expected response:
```json
{"status":"ok","timestamp":"2025-01-18T..."}
```

## Frontend Integration

Update your NT Prism frontend to use this backend:

```javascript
const BACKEND_URL = "https://your-app-name.onrender.com";

const response = await fetch(`${BACKEND_URL}/api/analyze`, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    model: "claude-sonnet-4-20250514",
    max_tokens: 4000,
    messages: [{
      role: "user",
      content: systemPrompt
    }]
  })
});

const data = await response.json();
```

## Cost Considerations

**Render Free Tier:**
- 750 hours/month free
- Service sleeps after 15 minutes of inactivity
- Cold start: ~30 seconds after sleep
- Perfect for development and light usage

**Claude API Costs:**
- Input: ~$3 per million tokens
- Output: ~$15 per million tokens
- Typical request (3 verses): ~$0.025 (2.5¢)
- Typical request (7 verses): ~$0.03-0.05 (3-5¢)

## Security Notes

- Never commit your `.env` file or API keys to Git
- API key is stored as an environment variable on Render
- CORS is enabled for all origins (restrict in production if needed)
- Consider adding rate limiting for production use

## Troubleshooting

**"CLAUDE_API_KEY not configured" error:**
- Verify environment variable is set in Render dashboard
- Redeploy after adding the variable

**504 Gateway Timeout:**
- Render free tier may be sleeping
- First request after sleep takes ~30 seconds
- Subsequent requests are fast

**CORS errors:**
- Should not occur with this proxy
- Check that frontend is calling backend URL, not Claude API directly

## Production Considerations

For production deployment:
- Use Render paid tier for always-on service ($7/month)
- Add rate limiting middleware
- Implement request logging
- Add authentication if exposing publicly
- Restrict CORS to specific domains

## License

MIT

## Support

For issues with:
- This backend: Open an issue in this repository
- Claude API: https://docs.anthropic.com/
- Render deployment: https://render.com/docs
