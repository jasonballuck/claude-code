# Timmified Workflow - Setup Guide

## Quick Start

This guide will help you deploy the Timmified Document Style Guide Processor workflow in n8n.

---

## Prerequisites

- n8n instance (v1.0 or higher)
- Microsoft 365 tenant with Teams and OneDrive
- OpenAI API key with GPT-4 access
- Admin access to configure OAuth apps

---

## Step 1: Install Dependencies

### Option A: n8n with npm (Self-hosted)
```bash
cd ~/.n8n/nodes
npm install adm-zip
```

### Option B: n8n Docker
Add to your Dockerfile:
```dockerfile
RUN cd /usr/local/lib/node_modules/n8n && npm install adm-zip
```

Or install after container starts:
```bash
docker exec -it n8n sh
cd /usr/local/lib/node_modules/n8n
npm install adm-zip
```

---

## Step 2: Configure Microsoft Teams App

1. Go to [Azure Portal](https://portal.azure.com) > Azure Active Directory > App registrations
2. Click **New registration**
3. **Name:** "n8n Timmified Bot"
4. **Supported account types:** Accounts in this organizational directory only
5. **Redirect URI:** `https://your-n8n-instance.com/rest/oauth2-credential/callback`
6. Click **Register**

### API Permissions
Add these permissions:
- `Channel.ReadBasic.All` (Application)
- `ChannelMessage.Send` (Delegated)
- Click **Grant admin consent**

### Credentials
- Copy **Application (client) ID**
- Create **Client secret** (Certificates & secrets)
- Copy secret value

---

## Step 3: Configure Microsoft OneDrive App

You can use the same Azure app or create a separate one:

### API Permissions (add to existing or new app)
- `Files.ReadWrite.All` (Delegated)
- Click **Grant admin consent**

---

## Step 4: Configure n8n Credentials

### Microsoft Teams OAuth2
1. In n8n: Credentials > New > Microsoft Teams OAuth2
2. **Name:** "Microsoft Teams Account"
3. **Client ID:** (from Step 2)
4. **Client Secret:** (from Step 2)
5. **Tenant ID:** (from Azure portal)
6. Click **Connect my account** and authorize

### Microsoft OneDrive OAuth2
1. In n8n: Credentials > New > Microsoft OneDrive OAuth2
2. **Name:** "Microsoft OneDrive Account"
3. **Client ID:** (same as Teams or separate)
4. **Client Secret:** (same as Teams or separate)
5. Click **Connect my account** and authorize

### OpenAI API
1. In n8n: Credentials > New > OpenAI API
2. **Name:** "OpenAI Account"
3. **API Key:** (your OpenAI key)
4. Save

---

## Step 5: Set Environment Variables

### Add to n8n environment
```bash
# Default Teams channel for errors (optional)
TEAMS_CHANNEL_ID=19:xxxxxxxxxxxxxxxxx@thread.tacv2

# Your corporate style guide instructions for OpenAI
OPENAI_STYLE_GUIDE_INSTRUCTIONS="You are a corporate style guide editor. Review and rewrite the following text to align with our corporate standards:

- Use active voice
- Keep sentences under 20 words
- Use \"we\" instead of \"the company\"
- Avoid jargon and buzzwords
- Use title case for headings
- Maintain professional but friendly tone
- Ensure consistent terminology

Preserve the original structure and formatting markers. Return only the edited text with the same paragraph breaks."
```

### For Docker
Add to `docker-compose.yml`:
```yaml
environment:
  - TEAMS_CHANNEL_ID=19:xxxxxxxxxxxxxxxxx@thread.tacv2
  - OPENAI_STYLE_GUIDE_INSTRUCTIONS=You are a corporate style guide editor...
```

### For self-hosted n8n
Add to `.env`:
```bash
export TEAMS_CHANNEL_ID="19:xxxxxxxxxxxxxxxxx@thread.tacv2"
export OPENAI_STYLE_GUIDE_INSTRUCTIONS="You are a corporate style guide editor..."
```

---

## Step 6: Import Workflow

1. In n8n: Workflows > Import from File
2. Select `timmified-workflow.json`
3. Click **Import**

### Update Credential IDs
The workflow references credential IDs that need to match your setup:

```json
"credentials": {
  "microsoftTeamsOAuth2Api": {
    "id": "1",  // Update to your Teams credential ID
    "name": "Microsoft Teams Account"
  }
}
```

Find your credential IDs:
- n8n UI > Credentials > Click on credential > Check URL for ID

Update these nodes:
- Teams Message Trigger
- Send Success Message to Teams
- Send Error to Teams
- Download File from OneDrive
- Upload to OneDrive
- OpenAI Style Guide Processing

---

## Step 7: Configure Teams Webhook

1. Open the "Teams Message Trigger" node
2. Click **Listen for Test Event**
3. Go to your Teams channel
4. Post a test message (e.g., "test")
5. n8n will capture the webhook URL
6. Click **Activate** workflow

---

## Step 8: Test the Workflow

### Test 1: Valid Word Document
1. Upload a `.docx` file to OneDrive
2. Copy the share link
3. Post in Teams: `Please Timmify this: [paste link]`
4. Expect: Success message with new file link in ~1-2 minutes

### Test 2: Valid PowerPoint
1. Upload a `.pptx` file to OneDrive
2. Copy the share link
3. Post in Teams: `Process this presentation: [paste link]`
4. Expect: Success message with new file link

### Test 3: Invalid Input
1. Post in Teams: `Please Timmify my document` (no link)
2. Expect: Error message explaining missing link

### Test 4: Unsupported File Type
1. Share a PDF or image link
2. Expect: Error message explaining supported file types

---

## Troubleshooting

### Error: "adm-zip not found"
**Solution:** Install adm-zip package (see Step 1)

### Error: "403 Forbidden" downloading file
**Solutions:**
- Verify OneDrive OAuth permissions
- Ensure file sharing is enabled
- Check if user has access to the file

### Error: "OpenAI API error"
**Solutions:**
- Verify API key is valid
- Check OpenAI account has credits
- Ensure GPT-4 access is enabled

### Error: "Cannot read environment variable"
**Solutions:**
- Verify environment variables are set
- Restart n8n after adding variables
- Check variable names match exactly

### Workflow doesn't trigger from Teams
**Solutions:**
- Ensure workflow is activated
- Check webhook URL is registered
- Verify Teams app permissions
- Test with a simple message first

### Processed file is corrupted
**Solutions:**
- Verify adm-zip is installed correctly
- Check if original file is valid
- Test with simpler documents first
- Review OpenXML extraction logs

---

## Monitoring & Maintenance

### Monitor These Metrics
- Execution time (should be <4 minutes)
- Success rate (aim for >95%)
- OpenAI API costs
- OneDrive storage usage

### Regular Tasks
- Review error logs weekly
- Update style guide instructions as needed
- Test with new document formats
- Monitor user feedback
- Update OpenAI model if needed (GPT-4 → GPT-4 Turbo, etc.)

---

## Customization

### Modify Style Guide Instructions
Update the `OPENAI_STYLE_GUIDE_INSTRUCTIONS` environment variable and restart n8n.

### Change Filename Format
Edit the "Generate Timestamped Filename" node:
```javascript
const newFilename = `${originalFilename}_Timmified_${timestamp}.${fileType}`;
// Change to:
const newFilename = `${originalFilename}_StyleGuide_${timestamp}.${fileType}`;
```

### Add More Error Categories
Edit the "Format Human-Friendly Error" node to add custom error messages.

### Support Additional File Types
Add file type validation in "Validate & Extract File Link":
```javascript
const isValidFileType = fileLink.match(/\.(docx|pptx|xlsx)(\\?|$)/);
```

Then add extraction logic for the new format.

---

## Security Best Practices

1. **Restrict OAuth scope** to minimum required permissions
2. **Use separate credentials** for prod vs test environments
3. **Rotate API keys** regularly (quarterly)
4. **Monitor API usage** for anomalies
5. **Limit Teams channels** that can trigger workflow
6. **Review processed files** periodically for sensitive data
7. **Enable n8n audit logging**
8. **Use HTTPS only** for n8n instance

---

## Support & Resources

- **Workflow File:** `timmified-workflow.json`
- **Testing Report:** `TIMMIFIED_TESTING_REPORT.md`
- **n8n Documentation:** https://docs.n8n.io
- **OpenXML Documentation:** https://docs.microsoft.com/en-us/office/open-xml

---

## Quick Reference

### Supported File Types
- `.docx` (Microsoft Word)
- `.pptx` (Microsoft PowerPoint)

### Output Filename Format
```
{original_name}_Timmified_{YYYYMMDDhhmm}.{extension}
```

### Performance Targets
- Execution: <4 minutes
- Concurrency: 2-3 requests/hour
- Success rate: >95%

### Required Permissions
- Teams: `Channel.ReadBasic.All`, `ChannelMessage.Send`
- OneDrive: `Files.ReadWrite.All`
- OpenAI: GPT-4 API access

---

**Last Updated:** 2025-12-16
**Workflow Version:** 1.0
