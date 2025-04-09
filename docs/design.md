# buysomepi.com Design Document

## PRIMARY GOAL FOR ALL DESIGN AND IMPLEMENTATION

- SELL NFTS
- SELL ADS
- DISPLAY ADS

## How it works

- Buy Some Pi (https://www.buysomepi.com) is the URL for the app
- Users can purchase a Pi Slice (a decimal position of pi) for a fixed price based on supply of 314,159 Solana-based NFTs 

| Tier              | Price    | Features                              | Example Positions        |
|-------------------|----------|---------------------------------------|--------------------------|
| **Random**        | $1.00    | Random slice from available pool      | 89793, 23846             |
| **Standard**      | $3.14    | Slice from available pool             | 352925, 57988            |
| **Patterned**     | $6.28    | Repeating digits (e.g., 11111)        | 33333, 88888             |
| **Mathematical**  | $31.41   | Prime positions, Fibonacci sequences  | 541 (100th prime), 1597  |
| **Cultural**      | $159.99  | Meme numbers (69, 420), years (1969)  | 31415, 1337              |
| **Ultra-Rare**    | $314.15  | First 1000 digits, famous constants   | 1415, 26535              |

- 50% chance that buysomepi.com mints our own randomly selected NFT with each user purchase
- Each **Standard** purchase for $3.14 enables a user to select an available standard Pi digit
- Each **Patterned** purchase for $6.28 enables a user to select an available repeating digit sequence
- NFT minted at time of purchase
- NFT entitles purchaser to advertise on the Pi Gallery
  - All ads subject to review
  - Ads limited to 140-character text with emoji support. 
  - No links, adult content, filter for bad words, auto-rejected if attempted with no refunds
- Resale of NFT transfers right to place ad in Pi Gallery

### Frontend Pages

1. Home Page (`/`)
   - Ticker bar showing app stats
     - Total Pi Slice Sold
     - Pi Slice Remaining
     - Latest Pi Slice Purchased (e.g., "Pi Slice #xxxxx just purchased!")
     - Total Active Ads
     - Total Ad Impressions Served
     - Number of Unique Pi Owners
     - Premium Pi Slice Remaining
     - Total Value Locked (USD Estimate - Sum of purchase prices)
     - Most Recently Added Ad
     - Random NFTs Minted by buysomepi.com
   - Search box for finding specific Pi Slice
   - Premium Pi Gallery (showcase of Premium Pi Numbers)
   - Pi Gallery (paged results, filterable, searchable)
   - NFT Cards (Previous, Latest, Featured, Random, Next)

2. Cart (`/cart`)
   - List of Pi Slice in cart
   - Bundle discount calculation
   - Bundle suggestions
   - Checkout flow
   - NFT Cards (Previous, Latest, Featured, Random, Next)

4. User Dashboard (`/dashboard`)
   - Owned Pi Slice
   - Ad management
   - Transaction history
   - Referral stats
   - NFT Cards (Previous, Latest, Featured, Random, Next)

5. Ad Manager (`/dashboard/ads/:index`)
   - Ad image upload
   - Target URL setting
   - Ad preview
   - Moderation status
   - NFT Cards (Previous, Latest, Featured, Random, Next)

6. Admin Panel (`/admin`)
   - Ad moderation queue
   - User management
   - Pi Slice reservation tool
   - Analytics dashboard

7. About Page (`/about`)

8. Terms of Service Page (`/terms`)

9. FAQ Page (`/faq`)

## Revenue

- Primary sales 1 USD per NFT + 5% secondary market royalties on all NFT resales.
- Price Tiers:
    * Standard Pi Slice: $1 each
    * Premium for special and notable numbers (fixed, varied pricing per NFT)
- Purchase random NFT for .5 USD
- Resell of randomly minted NFT by buysomepi.com

## Features

### Core Product Features

#### 1. Digital Pi Slice Ownership
- Own individual Pi Slice as NFTs on the Solana blockchain
- Each Pi Slice has a unique position in the Pi sequence, creating scarcity and uniqueness
- Ownership is verifiable on-chain and transferable via standard NFT marketplaces

#### 2. Premium Pi Numbers
- Special collection of mathematically, historically, or culturally significant Pi positions
- Fixed premium pricing based on significance and rarity
- Enhanced metadata with historical context and mathematical importance
- Featured placement in the Premium Pi Gallery on the homepage
- Special visual treatment for notable Pi Slice (golden borders, badges, etc.)

#### 3. Advertising Platform
- Place ads (can be any content, not just an "ad") on owned Pi Slice visible to site visitors
- Automated content moderation with Hive API
- Performance metrics including views and clicks

#### 4. Blockchain Integration
- Transparent on-chain verification of ownership
- Secure NFT minting through Metaplex Candy Machine
- Multiple payment options (credit card via Stripe, direct SOL payments)
- MoonPay integration for easy crypto onboarding

#### 5. Random Purchases
- User can hit "Feeling Lucky" button to purchase and mint a randomly selected NFT for .50 USD
- On every purchase, the system has a 50% chance to mint a randomly selected NFT (owned by buysomepi.com)

## Core Architecture

### Tech Stack
- Frontend: Next.js 14+ (React framework)
- Backend: Next.js API routes (serverless functions) with Vercel Edge Functions
- Database: Vercel Postgres
- Authentication: WalletConnect (for NFT owners), Clerk.com (for advertisers)
- Blockchain: Solana with Metaplex Candy Machine
- Infrastructure: Vercel Pro (leveraging built-in image optimization)
- Content Moderation: Hive Moderation API
- Payments: Stripe, Solana wallet transactions

### System Components
```
┌────────────────┐   ┌─────────────────┐   ┌───────────────────┐
│  Next.js App   │   │ Vercel Edge     │   │ Vercel Postgres   │
│  - React UI    │◄──┼─ Functions      │◄──┼─ - 1M Pi Slice     │
│  - Client      │   │ - CRUD APIs     │   │ - User data       │
│  - Public Site │   │ - Wallet Auth   │   │ - NFT records     │
└────────────────┘   └─────────────────┘   └───────────────────┘
        │                     │                      │
        ▼                     ▼                      │
┌────────────────┐   ┌─────────────────┐   ┌────────▼─────────┐
│ Authentication │   │ Solana Chain    │   │ Hive Moderation  │
│ - WalletConnect│   │ - NFT Mint      │   │ - Ad content     │
│ - Clerk.com    │   │ - Royalties     │   │   screening      │
└────────────────┘   └─────────────────┘   └──────────────────┘
                             ▲                      
                             │                      
                     ┌───────┴───────┐   ┌──────────────────────┐
                     │ Metaplex      │   │ Background Workers   │
                     │ Candy Machine │   │ - Batch operations   │
                     │ - NFT Creation│   │ - NFT card generation│
                     └───────────────┘   │ - Ad integration     │
                                         └──────────────────────┘
```

### Pi Data Source

*   API Source: The Pi API (api.pi.delivery) provides programmatic access to millions of Pi Slice. Example: `https://api.pi.delivery/v1/pi?start=1&numberOfSlices=10` returns "1415926535".
*   Data Storage: We'll store the full million Pi Slice in our database, with each Pi Slice having a unique index, NFT status, and advertising data. No need for on-the-fly calculations or external API dependencies after initial population.
*   Batch Processing: During setup, we'll fetch and store Pi data in batches (e.g., 1000 Pi Slice per API call) to populate our database efficiently.


### Database Schema

#### pi_slices
```sql
CREATE TABLE pi_slices (
  pi_index INTEGER PRIMARY KEY,
  pi_slice CHAR(1) NOT NULL,
  is_available BOOLEAN DEFAULT TRUE,
  price_tier VARCHAR(15) DEFAULT 'standard',
  owner_wallet_address VARCHAR(48) NULL,
  nft_mint_address VARCHAR(48) NULL,
  last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  price DECIMAL(10,2) NULL,
  bundle_id VARCHAR(36) NULL,
  bundle_position INTEGER NULL,
  is_reserved BOOLEAN DEFAULT FALSE,
  significance TEXT NULL,
  view_count INTEGER DEFAULT 0,
  feature_priority INTEGER NULL
);

CREATE INDEX idx_pi_slices_owner ON pi_slices(owner_wallet_address);
CREATE INDEX idx_pi_slices_availability ON pi_slices(is_available);
CREATE INDEX idx_pi_slices_price_tier ON pi_slices(price_tier);
CREATE INDEX idx_pi_slices_featured ON pi_slices(feature_priority);
```

#### ads
```sql
CREATE TABLE ads (
  ad_id SERIAL PRIMARY KEY,
  pi_index INTEGER NOT NULL REFERENCES pi_slices(pi_index),
  ad_image_url VARCHAR(255) NULL,
  ad_target_url VARCHAR(255) NULL,
  ad_status VARCHAR(15) DEFAULT 'none',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  moderation_notes TEXT NULL,
  view_count INTEGER DEFAULT 0,
  click_count INTEGER DEFAULT 0
);

CREATE INDEX idx_ads_pi_index ON ads(pi_index);
CREATE INDEX idx_ads_status ON ads(ad_status);
```

#### users
```sql
CREATE TABLE users (
  wallet_address VARCHAR(48) PRIMARY KEY,
  email VARCHAR(255) NULL,
  display_name VARCHAR(100) NULL,
  referrer_wallet_address VARCHAR(48) NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  last_login TIMESTAMP NULL,
  is_admin BOOLEAN DEFAULT FALSE
);

CREATE INDEX idx_users_referrer ON users(referrer_wallet_address);
```

#### transactions
```sql
CREATE TABLE transactions (
  transaction_id VARCHAR(64) PRIMARY KEY,
  wallet_address VARCHAR(48) NOT NULL REFERENCES users(wallet_address),
  transaction_type VARCHAR(20) NOT NULL,
  amount DECIMAL(10,2) NOT NULL,
  status VARCHAR(15) DEFAULT 'pending',
  pi_indexes INTEGER[] NULL,
  bundle_id VARCHAR(36) NULL,
  referrer_wallet_address VARCHAR(48) NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  payment_method VARCHAR(20) NULL,
  payment_tx_id VARCHAR(100) NULL,
  discount_applied DECIMAL(5,2) DEFAULT 0.00
);

CREATE INDEX idx_transactions_wallet ON transactions(wallet_address);
CREATE INDEX idx_transactions_bundle ON transactions(bundle_id);
```

### API Routes

#### Core APIs

1. `/api/slices`
   - `GET /api/slices/:index` - Get details for a specific Pi Slice
   - `GET /api/slices?query=...` - Search for Pi Slice
   - `POST /api/slices/:index/purchase` - Purchase a Pi Slice

2. `/api/cart`
   - `GET /api/cart` - Get current cart contents
   - `POST /api/cart` - Add Pi Slice to cart
   - `DELETE /api/cart/:index` - Remove Pi Slice from cart

3. `/api/ads`
   - `GET /api/ads/:index` - Get ad for a Pi Slice
   - `POST /api/ads/:index` - Create/update ad

4. `/api/users`
   - `GET /api/users/profile` - Get user profile
   - `POST /api/users/profile` - Update user profile
   - `GET /api/users/owned` - Get owned Pi Slice

5. `/api/admin`
   - `GET /api/admin/ads/pending` - Get pending ad reviews
   - `POST /api/admin/ads/:id/moderate` - Approve/reject ad
   - `POST /api/admin/reserve/:index` - Reserve Pi Slice
   - `GET /api/admin/premium` - Get Premium Pi Numbers list

6. `/api/nfts`
   - `GET /api/nfts/latest?count=4&page=0` - Get recently purchased NFTs with their ads
   - `GET /api/nfts/featured?count=4&page=0` - Get featured NFTs based on significance
   - `GET /api/nfts/random?count=4&page=0` - Get random NFTs from the collection
   - `POST /api/nfts/impression/:index` - Track ad impression
   - `POST /api/nfts/click/:index` - Track ad click

### API Specifications

#### GET /api/slices/:index

Request:
```
GET /api/slices/14159
```

Response (200 OK):
```json
{
  "pi_index": 14159,
  "pi_slice": "2",
  "is_available": true,
  "price_tier": "special",
  "price": 10.00,
  "context": "...65358979323846264338327950288419716939937510...",
  "highlight_index": 30
}
```

Response (404 Not Found):
```json
{
  "error": "Pi Slice not found"
}
```

#### POST /api/slices/:index/purchase

Request:
```
POST /api/slices/14159
Content-Type: application/json

{
  "wallet_address": "xyzABC123...",
  "payment_method": "stripe",
}
```

Response (200 OK):
```json
{
  "transaction_id": "tx_123456",
  "status": "processing",
  "payment_url": "https://stripe.com/pay/...",
  "redirect_url": "/dashboard"
}
```

### Solana Integration

#### NFT Metadata Structure
```json
{
  "name": "Pi Slice #14159",
  "symbol": "PIDI",
  "description": "The 14,159th Pi Slice: 2",
  "image": "https://buysomepi.com/nft/14159.png",
  "animation_url": "https://buysomepi.com/nft/14159.html",
  "external_url": "https://buysomepi.com/slice/14159",
  "attributes": [
    {"trait_type": "Pi Slice", "value": "2"},
    {"trait_type": "Position", "value": 14159},
    {"trait_type": "Price Tier", "value": "special"},
    {"trait_type": "Context", "value": "...6535897932384626433832795028841971693993751058..."}
  ],
  "properties": {
    "files": [
      {
        "uri": "https://buysomepi.com/nft/14159.png",
        "type": "image/png"
      }
    ],
    "category": "image",
    "creators": [
      {
        "address": "project_wallet_address",
        "share": 100
      }
    ]
  }
}
```

#### Minting Process with Metaplex Candy Machine

1. Initialize Candy Machine for batch minting
```javascript
async function setupCandyMachine() {
  const { candyMachine } = await metaplex.candyMachines().create({
    sellerFeeBasisPoints: 500, // 5% royalty
    itemsAvailable: 100, // Batch size
    collection: {
      name: "Pi Slice Collection",
      family: "Pi Slice"
    },
    guards: {
      solPayment: {
        amount: { basisPoints: 100000000, currency: { symbol: "SOL", decimals: 9 } },
        destination: new PublicKey("treasury_wallet")
      }
    }
  });
  
  return candyMachine.address.toString();
}
```

2. Add NFT items to Candy Machine
```javascript
async function addItemsToCandyMachine(candyMachineAddress, piIndexes) {
  const candyMachine = await metaplex.candyMachines().findByAddress({
    address: new PublicKey(candyMachineAddress),
  });
  
  const items = await Promise.all(piIndexes.map(async (piIndex) => {
    const metadata = generateMetadata(piIndex);
    const metadataUri = await uploadMetadata(metadata);
    
    return {
      name: `Pi Slice #${piIndex}`,
      uri: metadataUri,
    };
  }));
  
  await metaplex.candyMachines().insertItems({
    candyMachine,
    items,
  });
}
```

3. Mint NFT for user
```javascript
async function mintNFT(candyMachineAddress, walletAddress, piIndex) {
  const candyMachine = await metaplex.candyMachines().findByAddress({
    address: new PublicKey(candyMachineAddress),
  });
  
  const { nft } = await metaplex.candyMachines().mint({
    candyMachine,
    collectionUpdateAuthority: metaplex.identity(),
  });
  
  // Update database
  await db.execute(
    'UPDATE pi_slices SET is_available = false, owner_wallet_address = $1, nft_mint_address = $2 WHERE pi_index = $3',
    [walletAddress, nft.address.toString(), piIndex]
  );
  
  return nft.address.toString();
}
```

### Ad Moderation with Hive API

#### Content Moderation Flow

1. Advertiser uploads ad content
2. System sends content to Hive API for screening
3. Automated approval or flagging for manual review

```javascript
async function moderateAdContent(adId, imageUrl, targetUrl) {
  // Step 1: Check image with Hive API
  const imageResponse = await fetch('https://api.thehive.ai/api/v2/task/sync', {
    method: 'POST',
    headers: {
      'Authorization': `Token ${process.env.HIVE_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      image: { url: imageUrl },
      models: ['nudity', 'suggestive', 'gore', 'drugs', 'gambling']
    })
  });
  
  const imageResults = await imageResponse.json();
  
  // Step 2: Check target URL with Hive API
  const urlResponse = await fetch('https://api.thehive.ai/api/v2/task/sync', {
    method: 'POST',
    headers: {
      'Authorization': `Token ${process.env.HIVE_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      url: targetUrl,
      models: ['phishing', 'malware', 'scam']
    })
  });
  
  const urlResults = await urlResponse.json();
  
  // Step 3: Determine if content can be auto-approved
  const isAutoApproved = evaluateAutoApproval(imageResults, urlResults);
  
  // Step 4: Update database with moderation results
  const status = isAutoApproved ? 'approved' : 'pending_review';
  const moderationNotes = JSON.stringify({
    imageResults: imageResults.status,
    urlResults: urlResults.status,
    autoDecision: isAutoApproved
  });
  
  await db.execute(
    'UPDATE ads SET ad_status = $1, moderation_notes = $2, updated_at = CURRENT_TIMESTAMP WHERE ad_id = $3',
    [status, moderationNotes, adId]
  );
  
  return {
    status,
    isAutoApproved,
    reasonCodes: getReasonCodes(imageResults, urlResults)
  };
}

function evaluateAutoApproval(imageResults, urlResults) {
  // Implement decision logic based on API results
  // Auto-approve only if all checks pass with high confidence
  // Return boolean indicating if content can be auto-approved
}

function getReasonCodes(imageResults, urlResults) {
  // Extract reason codes from API results for displaying to advertisers or moderators
  // Return array of specific issues found, if any
}
```

#### Ad Moderation API Endpoints

1. `/api/ads/:index/moderate`
   - Automatically triggered when ad is submitted
   - Returns moderation status

2. `/api/admin/moderation-queue`
   - Lists ads requiring manual review
   - Provides moderation tools for admin users

### Fixed Price Notable Pi Slice

#### Premium Pi-Related Numbers

Digits and positions in Pi that have special mathematical, historical, or cultural significance, offered at fixed premium prices.

- Dedicated showcase page for Premium Pi Numbers
- Historical context and significance displayed with each digit
- First come, first served
- Special NFT metadata highlighting web significance

```javascript
const premiumPiNumbers = [
  // Famous Pi Positions
  { position: 1, significance: "First digit of Pi (1)", price: 250.00 },
  { position: 2, significance: "Second digit of Pi (4)", price: 200.00 },
  { position: 3, significance: "Third digit of Pi (1)", price: 200.00 },
  { position: 10, significance: "Tenth digit of Pi", price: 100.00 },
  { position: 100, significance: "Hundredth digit of Pi", price: 150.00 },
  { position: 1000, significance: "Thousandth digit of Pi", price: 200.00 },
  { position: 10000, significance: "Ten thousandth digit of Pi", price: 250.00 },
  { position: 100000, significance: "Hundred thousandth digit of Pi", price: 300.00 },
  
  // Mathematical Constants in Pi
  { position: 31415, significance: "Pi × 10000 position", price: 150.00 },
  { position: 27182, significance: "e × 10000 position (Euler's number)", price: 150.00 },
  { position: 16180, significance: "φ × 10000 position (Golden Ratio)", price: 150.00 },
  { position: 14142, significance: "√2 × 10000 position", price: 120.00 },
  { position: 17320, significance: "√3 × 10000 position", price: 120.00 },
  { position: 20000, significance: "√4 × 10000 position", price: 120.00 },
  
  // Famous Mathematical Sequences
  { position: 762, significance: "Feynman Point - six 9s in a row", price: 350.00 },
  { position: 1852, significance: "Five consecutive 4s in Pi", price: 200.00 },
  { position: 6676, significance: "Five consecutive 1s in Pi", price: 200.00 },
  { position: 9013, significance: "Five consecutive 3s in Pi", price: 200.00 },
  { position: 6788, significance: "Sequence 7415926", price: 150.00 },
  { position: 17387, significance: "Consecutive digits 67890", price: 200.00 },
  { position: 44899, significance: "Seven consecutive 8s in Pi", price: 250.00 },
  { position: 60599, significance: "Six consecutive 5s in Pi", price: 225.00 },
  { position: 123456, significance: "Palindromic position 123456", price: 200.00 },
  
  // Fibonacci Sequence Appearances
  { position: 24646, significance: "Consecutive Fibonacci sequence 1123", price: 175.00 },
  { position: 12843, significance: "Fibonacci numbers 13 21", price: 150.00 },
  { position: 29617, significance: "Fibonacci pattern 235", price: 150.00 },
  
  // Historical Dates in Pi
  { position: 1776, significance: "US Declaration of Independence year", price: 177.60 },
  { position: 1789, significance: "French Revolution year", price: 150.00 },
  { position: 1865, significance: "End of US Civil War", price: 150.00 },
  { position: 1905, significance: "Einstein's Miracle Year", price: 150.00 },
  { position: 1945, significance: "End of World War II", price: 150.00 },
  { position: 1969, significance: "First Moon Landing", price: 196.90 },
  
  // Important Birth Years
  { position: 1452, significance: "Leonardo da Vinci's birth year", price: 145.20 },
  { position: 1564, significance: "Galileo Galilei's birth year", price: 150.00 },
  { position: 1642, significance: "Isaac Newton's birth year", price: 164.20 },
  { position: 1707, significance: "Euler's birth year", price: 150.00 },
  { position: 1879, significance: "Einstein's birth year", price: 187.90 },
  { position: 1887, significance: "Ramanujan's birth year", price: 150.00 },
  
  // Pi Day References
  { position: 314, significance: "Pi Day (3/14)", price: 314.00 },
  { position: 3141, significance: "Extended Pi Day (3.141)", price: 200.00 },
  { position: 31415, significance: "Complete Pi Day (3.14159)", price: 250.00 },
  
  // Tech-Related Numbers
  { position: 80, significance: "HTTP default port", price: 80.00 },
  { position: 443, significance: "HTTPS default port", price: 100.00 },
  { position: 404, significance: "HTTP Not Found status code", price: 125.00 },
  { position: 200, significance: "HTTP OK status code", price: 100.00 },
  { position: 1024, significance: "2^10 (Binary milestone)", price: 102.40 },
  { position: 2048, significance: "2^11 (Binary milestone)", price: 120.00 },
  { position: 4096, significance: "2^12 (Binary milestone)", price: 120.00 },
  
  // Visually Interesting Patterns
  { position: 1929, significance: "Continuous sequence 999999", price: 199.90 },
  { position: 7072, significance: "Repeating pattern 7777", price: 150.00 },
  { position: 9301, significance: "Symmetrical pattern 11211", price: 150.00 },
  { position: 12345, significance: "Sequential digits 12345", price: 250.00 },
  { position: 16360, significance: "Palindrome 94449", price: 175.00 },
  { position: 39999, significance: "Repeating 9s followed by 5", price: 150.00 },
  
  // Famous Mathematician References
  { position: 1596, significance: "Descartes' birth year", price: 150.00 },
  { position: 1646, significance: "Leibniz's birth year", price: 150.00 },
  { position: 1749, significance: "Laplace's birth year", price: 150.00 },
  { position: 1777, significance: "Gauss's birth year", price: 177.70 },
  { position: 1882, significance: "Emmy Noether's birth year", price: 150.00 },
  { position: 1854, significance: "Riemann's conjecture year", price: 150.00 },
  
  // Cosmic Numbers
  { position: 13699, significance: "Age of universe (13.699 billion years)", price: 150.00 },
  { position: 299792, significance: "Speed of light in vacuum (km/s)", price: 175.00 },
  { position: 6672, significance: "Gravitational constant first 4 digits", price: 150.00 },
  { position: 6626, significance: "Planck's constant first 4 digits", price: 150.00 },
  
  // Cultural References
  { position: 42, significance: "Hitchhiker's Guide to the Galaxy 'Answer'", price: 142.00 },
  { position: 314159, significance: "First 6 digits of Pi as position", price: 500.00 },
  { position: 271828, significance: "First 6 digits of e as position", price: 300.00 },
  { position: 577215, significance: "First 5 digits of Golden Ratio × 100000", price: 250.00 },
  { position: 161803, significance: "Golden Ratio × 100000", price: 200.00 },
  
  // Prime Number Positions
  { position: 73939, significance: "10000th prime number", price: 175.00 },
  { position: 9973, significance: "1000th prime number", price: 150.00 },
  { position: 541, significance: "100th prime number", price: 125.00 },
  { position: 997, significance: "168th prime number", price: 125.00 },
  { position: 31, significance: "11th prime number", price: 100.00 },
  
  // Famous Numbers in Science
  { position: 23, significance: "Chromosomes in human DNA", price: 123.00 },
  { position: 6022, significance: "Avogadro's number exponent", price: 150.00 },
  { position: 137, significance: "Fine structure constant approx (1/137)", price: 137.00 },
  { position: 273, significance: "Absolute zero in Celsius (-273.15°C)", price: 127.30 },
  { position: 8314, significance: "Universal gas constant rounded", price: 150.00 }
];
```

## Style Guide

This style approach maintains the mathematical clarity and professionalism of minimalism while incorporating just enough playfulness to make the experience engaging and memorable without becoming childish or overwhelming.

### Core Design Philosophy
A clean, minimalist foundation that prioritizes mathematical precision, with strategically placed playful elements that bring personality and gamification without overwhelming the interface.

### Color Palette
- **Primary Background**: Clean white (#FFFFFF)
- **Secondary Background**: Light grey (#F5F5F7) for card backgrounds
- **Primary Text**: Deep black (#121212)
- **Accent Blue**: Vibrant but clean blue (#0055FF) for interactive elements
- **Accent Yellow**: Warm yellow (#FFD100) for highlighting special digits
- **Accent Green**: Success green (#00C853) for confirmations
- **Special Digits**: Each price tier gets a subtle gradient:
  - Standard: Light blue gradient (#E6F0FF → #FFFFFF)
  - Special: Light yellow gradient (#FFFDE7 → #FFFFFF)
  - Famous: Light purple gradient (#F3E5F5 → #FFFFFF)

### Typography
- **Headings**: Modern geometric sans-serif (e.g., Poppins)
- **Body Text**: Clean, highly legible sans-serif (e.g., Inter)
- **Pi Slice**: Monospaced font (e.g., JetBrains Mono) to emphasize the mathematical nature

### Type Logo

> BUY SOME π

- Simple
- No specialized π design
- Don't overuse π symbol, keep it low-key

### Interface Elements
- **Cards**: Clean white cards with subtle shadows and rounded corners (8px radius)
- **Buttons**: Minimalist buttons with clear states (hover/pressed)
- **Inputs**: Simple, clearly defined input fields
- **Spacing**: Consistent 8px grid system for precise alignment

### Playful Elements
- **Pi Slice Characters**: Each Pi Slice (0-9) has a subtle, minimalist "personality"
  - Simple expressions using just dots and lines
  - Subtle animations on hover/interaction
  - The personalities become more pronounced on special/famous Pi Slice
- **Micro-interactions**: 
  - Pi Slice subtly react when searched or selected
  - Playful success animations when purchasing (confetti that quickly fades)
  - Smooth transitions between states

### Featured Pi Slice Display
- Grid of Pi Slice with subtle personality indicators
- Special/famous Pi Slice glow softly or have subtle animated backgrounds
- Clean data visualization showing Pi Slice position in Pi sequence

### Mobile Experience
- Touch targets optimized for mobile (minimum 44px)
- Bottom navigation with playful micro-animations
- Pull-to-refresh with mathematical animation (Pi symbol drawing itself)

### Loading States
- Minimal loading spinner based on Pi symbol
- Playful loading messages referencing mathematics and Pi

## Testing

### Unit Tests
- Database schema validation
- Price calculation functions
- Bundle discount eligibility

### Integration Tests
- Purchase flow end-to-end
- Cart management
- Ad submission and moderation
- User authentication

### E2E Tests
- Complete purchase journey
- Ad creation and display
- Bundle purchases

## Deployment

### Environment Variables
```
# Database
DATABASE_URL=postgres://...

# Authentication
CLERK_PUBLISHABLE_KEY=pk_...
CLERK_SECRET_KEY=sk_...
CLERK_SIGN_IN_URL=/advertiser/sign-in
CLERK_SIGN_UP_URL=/advertiser/sign-up
CLERK_AFTER_SIGN_IN_URL=/dashboard

# Blockchain
WALLET_PRIVATE_KEY=...
SOLANA_RPC_URL=...
CANDY_MACHINE_ID=...

# Payments
STRIPE_SECRET_KEY=...
STRIPE_WEBHOOK_SECRET=...

# Content Moderation
HIVE_API_KEY=...

# General
NEXT_PUBLIC_SITE_URL=https://buysomepi.com
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=...
```

### Build and Deploy Commands
```bash
# Install dependencies
npm install

# Build application
npm run build

# Deploy to Vercel
vercel --prod
```

