# Azure Active Directory Authentication Setup

This guide explains how to configure Azure Active Directory (AAD) authentication for your Azure Static Web App.

## Option 1: Configure via Azure Portal (Recommended)

1. **Create App Registration in Azure AD:**
   - Go to [Azure Portal](https://portal.azure.com)
   - Navigate to **Azure Active Directory** → **App registrations**
   - Click **New registration**
   - Name: `Your Static Web App Name`
   - Supported account types: Choose based on your needs
   - Redirect URI: 
     - Platform: **Single-page application (SPA)**
     - URI: `https://<your-app-name>.azurestaticapps.net/.auth/login/aad/callback`
   - Click **Register**

2. **Configure Static Web App Authentication:**
   - Go to your Static Web App in Azure Portal
   - Navigate to **Authentication** in the left menu
   - Click **Add identity provider**
   - Select **Microsoft**
   - Choose the app registration you created
   - Azure will automatically configure the settings

3. **Add Application Settings:**
   - In your Static Web App, go to **Configuration** → **Application settings**
   - Add these settings (they may be auto-populated):
     - `AZURE_CLIENT_ID`: Your App Registration's Application (client) ID
     - `AZURE_CLIENT_SECRET`: Create a client secret in your App Registration and add it here

## Option 2: Configure via staticwebapp.config.json

If you prefer to configure authentication in the config file:

1. **Get your Tenant ID:**
   - In Azure Portal, go to **Azure Active Directory** → **Overview**
   - Copy the **Tenant ID**

2. **Update staticwebapp.config.json:**
   - Replace `{tenantId}` in the `openIdIssuer` field with your actual tenant ID
   - Example: `"openIdIssuer": "https://login.microsoftonline.com/12345678-1234-1234-1234-123456789012/v2.0"`

3. **Add Application Settings in Azure Portal:**
   - Still add `AZURE_CLIENT_ID` and `AZURE_CLIENT_SECRET` as application settings

## Testing Authentication

1. Deploy your app to Azure Static Web Apps
2. Navigate to your app URL
3. Click "Login with Azure AD"
4. You should be redirected to Microsoft login
5. After successful login, you'll be redirected back and can call the API

## Local Development

For local development with authentication, use the Azure Static Web Apps CLI:

```bash
npm install -g @azure/static-web-apps-cli
swa start http://localhost:3000 --api-location api
```

This emulates the authentication flow locally.

## Troubleshooting

- **401 Unauthorized**: Make sure authentication is configured in Azure Portal and application settings are correct
- **Redirect URI mismatch**: Ensure the redirect URI in your App Registration matches your Static Web App URL
- **User not authenticated**: Check that the route protection in `staticwebapp.config.json` requires `"authenticated"` role

