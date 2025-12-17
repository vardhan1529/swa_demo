# Azure Static Web App with React and Azure Functions

A simple React application integrated with Azure Functions for Azure Static Web Apps, secured with Azure Active Directory (AAD) authentication.

## Project Structure

```
azure_swa/
├── api/                    # Azure Functions
│   ├── HttpTrigger/       # HTTP trigger function
│   ├── host.json          # Functions host configuration
│   └── package.json       # Functions dependencies
├── public/                # Static files
├── src/                   # React source code
│   ├── App.js            # Main React component
│   ├── App.css           # Component styles
│   ├── index.js          # React entry point
│   └── index.css         # Global styles
├── staticwebapp.config.json  # Azure SWA configuration
└── package.json          # React app dependencies
```

## Features

- **React App**: Modern React application with a simple UI
- **Azure Functions**: HTTP trigger that returns JSON responses
- **AAD Authentication**: Azure Active Directory authentication required for API access
- **API Integration**: React app calls the Azure Function on button click (requires authentication)

## Local Development

### Prerequisites

- Node.js (v14 or higher)
- Azure Functions Core Tools (for local function testing)

### Setup

1. Install React app dependencies:
   ```bash
   npm install
   ```

2. Install Azure Functions Core Tools (if not already installed):
   ```bash
   npm install -g azure-functions-core-tools@4
   ```

3. Start the React development server:
   ```bash
   npm start
   ```

4. In a separate terminal, start Azure Functions locally:
   ```bash
   cd api
   func start
   ```

5. Update the API call in `src/App.js` to use `http://localhost:7071/api/HttpTrigger` for local development.

## Deployment to Azure

1. Connect your repository to Azure Static Web Apps
2. Configure build settings:
   - **App location**: `/`
   - **Api location**: `/api`
   - **Output location**: `build`

3. Configure Azure Active Directory authentication:
   - In the Azure Portal, go to your Static Web App
   - Navigate to **Authentication** settings
   - Click **Add** to add an identity provider
   - Select **Microsoft** (Azure Active Directory)
   - Configure the app registration:
     - Create a new app registration or use an existing one
     - Set the redirect URL to: `https://<your-app-name>.azurestaticapps.net/.auth/login/aad/callback`
   - Copy the **Application (client) ID** and **Client secret value**
   - In your Static Web App, go to **Configuration** → **Application settings**
   - Add the following application settings:
     - `AZURE_CLIENT_ID`: Your Azure AD Application (client) ID
     - `AZURE_CLIENT_SECRET`: Your Azure AD Client secret value
   - Update `staticwebapp.config.json` with your Azure AD tenant ID (replace `{tenantId}`)

4. Azure will automatically build and deploy your app when you push to the main branch.

## API Endpoint

The Azure Function is available at `/api/HttpTrigger` and requires authentication:
- **Method**: GET or POST
- **Authentication**: Required (Azure Active Directory)
- **Query Parameters**: `name` (optional)
- **Response**: JSON object with message, timestamp, user information, and request details

## Authentication Flow

1. User clicks "Login with Azure AD" button
2. User is redirected to Azure AD login page
3. After successful authentication, user is redirected back to the app
4. User information is displayed and API calls are now authorized
5. The API function receives authenticated user information via the `x-ms-client-principal` header

## Example API Response

```json
{
  "message": "Hello, User!",
  "timestamp": "2024-01-01T12:00:00.000Z",
  "method": "GET",
  "query": {},
  "success": true,
  "user": {
    "userId": "user-id-here",
    "userDetails": "user@example.com",
    "identityProvider": "aad",
    "claims": [...]
  }
}
```

## Local Development with Authentication

For local development, you can use the Azure Static Web Apps CLI (`swa`) which provides authentication emulation:

```bash
npm install -g @azure/static-web-apps-cli
swa start http://localhost:3000 --api-location api
```

This will start the app with authentication support locally.

