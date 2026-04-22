![botvid](https://github.com/user-attachments/assets/94845f28-3f77-4ca7-8f04-43f3ee45d87b)


# ShopBot — Preview and Merchant Guide

ShopBot is a Telegram-based storefront for selling physical goods with Bitcoin payments. Customers browse and buy entirely inside Telegram. You manage everything — products, orders, wallets, and settings — through a private web-based admin panel.

---

## How It Works (Overview)

```
Customer opens Telegram bot
        ↓
Browses categories & products
        ↓
Selects item → sees BTC price + wallet address + QR code
        ↓
Sends exact BTC amount → bot confirms after N blockchain confirmations
        ↓
Customer submits shipping address
        ↓
Order appears in your Admin Panel → you ship → mark delivered
```

---

## Admin Panel

Access the admin panel in any browser at `http://your-server:5000`. Log in with the credentials you created on first setup.
<img width="1872" height="873" alt="image" src="https://github.com/user-attachments/assets/4b34cd28-1eab-4624-a15b-d759d701a875" />

### Dashboard

The dashboard gives you a live snapshot of your store:

| Section | What it shows |
|---|---|
| **Orders Awaiting Fulfillment** | New, unpaid or unshipped orders that need action |
| **In Transit** | Orders you have marked as shipped |
| **Order History** | Delivered and cancelled orders |
| **Recent Products** | Your latest listings |
| **Stats** | Pending orders, total orders, revenue |

Each order row has an **Action** dropdown (View / Edit / Delete) and a **Refresh Bot** button to push product changes to Telegram immediately.

---
<img width="1347" height="892" alt="image" src="https://github.com/user-attachments/assets/46966683-433d-45ad-a3c1-67f900458564" />

### Products

Add, edit, and delete your product listings. Each product has:

- **Name, Description, Price (USD)** — displayed to buyers in Telegram
- **Category** — groups products in the bot menu
- **Stock** — optional; displayed to buyers
- **Weight / Dimensions** — optional metadata
- **Image** — shown in the bot (JPEG, PNG, GIF, WebP, up to 16 MB)

> Changes go live in Telegram after you click **Refresh Bot** or when the next buyer browses.

---
<img width="1692" height="652" alt="image" src="https://github.com/user-attachments/assets/47259d6e-78f8-4d37-9797-42f6a564062f" />

### Categories

Organise products into categories (e.g. *Apparel*, *Electronics*, *Prints*). Buyers see categories as the first menu when they open the bot. Each category can have its own dedicated BTC wallet address (see Settings).

---
<img width="1849" height="765" alt="image" src="https://github.com/user-attachments/assets/92f26a12-1584-4401-976f-9008b56f5a09" />

### Orders

The Orders page is the fulfilment hub. For each order you can see:

- Order ID, date, customer Telegram username and ID
- Product purchased, price paid
- Shipping address *(hidden by default — click Show Addresses or enter your address password)*
- Status badge: **Pending → Shipped → Delivered**
- Tracking number (optional)
- Internal notes (visible only to you) and customer notes (sent to buyer via Telegram)

**Status workflow:**

1. New paid order arrives → status **Pending**
2. You ship the item → click **Mark Shipped**, optionally enter tracking number → customer gets a Telegram notification
3. Item arrives → click **Mark Delivered** → customer gets a confirmation message

You can also **Cancel** any active order or **Delete** orders you no longer need. Orders export to CSV via the **Export CSV** button.

---
<img width="1355" height="817" alt="image" src="https://github.com/user-attachments/assets/5f91cfe9-3983-46c9-b633-b8c9cf4be45b" />

### Settings

#### BTC Wallet Addresses

Set one or more Bitcoin receiving wallets. Assign a specific wallet to each product category so revenue from different product lines lands in separate wallets. Any category without a dedicated wallet falls back to the **Default** wallet.

```
Default wallet:        bc1q...
Electronics wallet:    bc1q...
Apparel wallet:        bc1q...
```
<img width="1018" height="352" alt="image" src="https://github.com/user-attachments/assets/23c66560-10ad-47b7-96c4-0ea8984aa2be" />

#### Blockchain Confirmations

Set how many on-chain confirmations are required before an order is considered paid (1–10). Lower = faster checkout experience. Higher = more protection against double-spend attacks.

| Confirmations | ~Wait time | Recommended for |
|---|---|---|
| 1 | ~10 min | Low-value items |
| 3 | ~30 min | Most physical goods |
| 6 | ~60 min | High-value items |

#### Address Viewing Password

Optionally protect customer shipping addresses with a secondary password. When set, addresses on the Orders page are hidden until you enter this password — separate from your login. Useful if multiple people share access to the admin panel.

#### Admin Users

Create additional admin accounts for team members. Each account has its own username and password. You can change passwords or remove accounts at any time. The last remaining account cannot be deleted.

---
<img width="1950" height="1514" alt="image" src="https://github.com/user-attachments/assets/337e4c74-7e23-48ab-a977-cf16d728ee57" />

## Customer Experience (Telegram)

When a customer opens your bot:

1. **Browse** — they see your categories, then products within each
2. **Select** — product image, description, price, and stock are displayed
3. **Checkout** — bot shows:
   - Exact BTC amount to send (locked to the live rate at time of purchase)
   - Your wallet address as text
   - A **scannable QR code** — opens directly in any Bitcoin wallet app (BlueWallet, Muun, Exodus, etc.) and auto-fills the address and amount
4. **Confirm** — bot polls the blockchain automatically every 60 seconds; buyer can also tap **Check Payment Status** manually
5. **Address collection** — once payment confirms, bot prompts buyer for their shipping address
6. **Updates** — buyer receives Telegram messages when their order is marked Shipped (with tracking number if provided) and Delivered

---

## Deployment Quick Reference

```bash
./deploy.sh              # Start bot + admin panel
./deploy.sh --bot-only   # Bot only
./deploy.sh --ui-only    # Admin panel only
```

**Required `.env` variables:**

```env
TELEGRAM_BOT_TOKEN=your_token_here
BTC_ADDRESS=your_default_wallet_here
FLASK_SECRET_KEY=a_long_random_string
FLASK_PORT=5000
```

> On first launch with no users configured, the admin panel redirects to a setup page where you create the initial admin account.

---

## Tips

- **Refresh Bot** after adding or editing products — changes are not instant without it.
- Set a **address viewing password** if you have staff who need order access but should not see customer addresses.
- Use **Internal Notes** on orders to track fulfilment details. Use **Customer Notes** to send messages directly to the buyer via Telegram.
- The CSV export is useful for reconciling orders with your shipping provider.
- BTC amounts are locked at the rate when the buyer initiates checkout — you receive exactly what was agreed even if the rate moves during confirmation.
