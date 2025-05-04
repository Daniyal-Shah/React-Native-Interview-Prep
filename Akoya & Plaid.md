### **1. Plaid in React Native**
#### **Definition**
Plaid is a **financial data aggregator API** that provides secure connectivity between apps and users' bank accounts (used by apps like Venmo, Robinhood).

#### **Purpose**
- **Account linking**: Connect to 11,000+ banks globally
- **Transaction fetching**: Retrieve historical and real-time transactions
- **Balance checks**: Get current account balances
- **Identity verification**: Confirm account ownership

#### **React Native Implementation**
```javascript
// Installation
yarn add react-native-plaid-link-sdk
cd ios && pod install

// Configuration (Android)
// android/app/src/main/AndroidManifest.xml
<intent-filter>
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="yourapp" />
</intent-filter>
```

**Full Connection Flow:**
```javascript
import { PlaidLink } from 'react-native-plaid-link-sdk';
import { Linking } from 'react-native';

const BankConnectionScreen = () => {
  const handleSuccess = async (publicToken) => {
    // Exchange public token for access token
    const response = await fetch('https://your-backend.com/exchange_token', {
      method: 'POST',
      body: JSON.stringify({ public_token: publicToken }),
    });
    const { accessToken } = await response.json();
    
    // Store securely
    await SecureStorage.setItem('plaid_access_token', accessToken);
  };

  return (
    <PlaidLink
      publicKey="YOUR_PUBLIC_KEY"
      env="sandbox" // or 'development'/'production'
      product={["auth", "transactions"]}
      onSuccess={handleSuccess}
      onExit={(error) => console.error('User exited:', error)}
      token="GENERATED_LINK_TOKEN" // From your backend
    >
      <Text>Connect Your Bank</Text>
    </PlaidLink>
  );
};

// Handle OAuth redirects
Linking.addEventListener('url', ({ url }) => {
  if (url.includes('plaid_link')) {
    // Handle OAuth return flow
  }
});
```

**Key Technical Considerations:**
1. **Token Rotation**:
   ```javascript
   // Refresh expired tokens
   const refreshToken = async () => {
     const response = await fetch('https://your-backend.com/refresh', {
       headers: { 'Plaid-Version': '2020-09-14' }
     });
     return response.json();
   };
   ```

2. **WebView Fallback**:
   ```javascript
   // For banks requiring OAuth
   <PlaidLink
     oauthRedirectUri="yourapp://oauth-redirect"
     oauthNonce="RANDOM_STRING"
   />
   ```

---

### **2. Akoya in React Native**
#### **Definition**
Akoya is an **API-based financial data network** that provides secure access to banking data without credential sharing (used by large FIs like Chase, Citi).

#### **Purpose**
- **Regulatory compliance**: FDX, SOC2 certified data flows
- **Enterprise-grade connectivity**: Direct bank APIs (no screen scraping)
- **Data quality**: Normalized transaction categorization

#### **React Native Implementation**
```javascript
// Installation (REST API approach)
yarn add axios

// Auth Flow
const startAkoyaConnection = async () => {
  // 1. Get authorization URL from backend
  const { authUrl } = await axios.post('https://your-backend.com/akoya/auth', {
    userId: 'user_123',
    permissions: ['ACCOUNTS_READ', 'TRANSACTIONS_READ']
  });

  // 2. Launch bank auth
  Linking.openURL(authUrl); 
};

// Handle callback
Linking.addEventListener('url', async ({ url }) => {
  if (url.includes('akoya_callback')) {
    const code = url.split('code=')[1];
    const { accessToken } = await axios.post(
      'https://api.akoya.com/v2/token',
      { code, grant_type: 'authorization_code' }
    );
    await SecureStorage.setItem('akoya_access_token', accessToken);
  }
});
```

**Data Fetching Example:**
```javascript
const fetchTransactions = async () => {
  const accessToken = await SecureStorage.getItem('akoya_access_token');
  
  const response = await axios.get(
    'https://api.akoya.com/v2/transactions',
    {
      headers: { 'Authorization': `Bearer ${accessToken}` },
      params: {
        start_date: '2023-01-01',
        end_date: '2023-12-31'
      }
    }
  );

  return response.data.transactions.map(tx => ({
    id: tx.transaction_id,
    amount: tx.amount,
    date: new Date(tx.date),
    merchant: tx.merchant.name
  }));
};
```

**Key Differences in Architecture:**

| Component          | Plaid                          | Akoya                          |
|--------------------|--------------------------------|--------------------------------|
| **Auth Flow**      | SDK handles OAuth              | Manual OAuth2.0 implementation |
| **Data Freshness** | ~5 min delay                   | Real-time via bank APIs        |
| **Error Handling** | SDK provides error codes       | HTTP status codes              |
| **Pricing**        | Pay-per-request                | Enterprise contracts           |

---

### **Advanced Scenarios**
#### **1. Handling Bank MFA (Multi-Factor Auth)**
**Plaid:**
```javascript
<PlaidLink
  onEvent={(event) => {
    if (event.eventName === 'HANDOFF') {
      // Show MFA input UI
    }
  }}
/>
```

**Akoya:**
```javascript
// Poll for MFA status
const checkMFAStatus = async (requestId) => {
  const { status } = await axios.get(
    `https://api.akoya.com/v2/requests/${requestId}`
  );
  if (status === 'MFA_REQUIRED') {
    // Show MFA modal
  }
};
```

#### **2. Data Sync Optimization**
```javascript
// Using React Query for Plaid
const { data } = useQuery(['transactions'], fetchPlaidData, {
  staleTime: 1000 * 60 * 5, // 5 minutes
  onError: (error) => {
    if (error.code === 'ITEM_LOGIN_REQUIRED') {
      // Re-authenticate
    }
  }
});

// Using Akoya with WebSockets
const socket = new WebSocket('wss://api.akoya.com/v2/realtime');
socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  if (data.event === 'TRANSACTION_POSTED') {
    // Update UI in real-time
  }
};
```

---

### **Security Best Practices**
1. **Token Storage**:
   ```javascript
   // Using react-native-keychain
   import Keychain from 'react-native-keychain';

   await Keychain.setGenericPassword(
     'plaid_token',
     accessToken,
     { service: 'com.yourapp.tokens' }
   );
   ```

2. **Certificate Pinning**:
   ```javascript
   // In react-native.config.js
   module.exports = {
     dependencies: {
       'react-native-plaid-link-sdk': {
         platforms: {
           android: {
             packageImportPath: 'import com.plaid.PlaidPackage;',
             packageInstance: 'new PlaidPackage()',
             buildTypes: 'release',
             https: {
               pinning: {
                 include: '*.plaid.com'
               }
             }
           }
         }
       }
     }
   };
   ```

3. **Audit Logging**:
   ```javascript
   // Log all financial API calls
   const auditLog = (action, metadata) => {
     Sentry.captureMessage(`FINANCE_API_${action}`, {
       tags: { userId: auth.currentUser.uid },
       extra: metadata
     });
   };
   ```

---

### **When to Use Each**
- **Choose Plaid If**:
  - You need quick integration
  - Your app targets consumers
  - You need global bank coverage

- **Choose Akoya If**:
  - Building for enterprise clients
  - Regulatory compliance is critical
  - You prefer API-first architecture

---

### **Interview Prep Cheatsheet**
**Common Questions:**
1. _"How would you handle token expiration?"_
   ```javascript
   // With axios interceptors
   axios.interceptors.response.use(null, async (error) => {
     if (error.response.status === 401) {
       const newToken = await refreshToken();
       error.config.headers.Authorization = `Bearer ${newToken}`;
       return axios.request(error.config);
     }
     return Promise.reject(error);
   });
   ```

2. _"Compare data freshness approaches"_  
   - Plaid: Webhooks + periodic sync  
   - Akoya: WebSocket streams + event-driven updates  

3. _"How would you implement joint account handling?"_  
   ```javascript
   // Plaid
   <PlaidLink
     product={["auth", "identity"]}
     accountFilters={{ subtypes: ["checking", "savings"] }}
   />

   // Akoya
   GET /v2/accounts?ownership_type=JOINT
   ``` 

Would you like me to simulate a full banking integration code review session? This could cover:
1. Error boundary implementation
2. Loading state optimization
3. Regulatory compliance checks
