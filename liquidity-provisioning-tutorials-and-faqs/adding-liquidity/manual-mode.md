---
icon: user
---

# Manual Mode

## Step-by-Step Guide: Adding Liquidity (Manual Mode)

### 1. Choose a Pool

Navigate to the **‘Pools’** section and select the trading pair you wish to provide liquidity to (e.g., `TOKEN_A / TOKEN_B`).

> 💡 If the pool doesn't exist yet, you can create one by clicking **‘Create Pool’**. You’ll supply assets in a roughly 50/50 value ratio to initialize the pool and select your preferred fee tier (more on fee tiers in a later section). Pool creation is permissionless and free.

On the pools' page, you’ll see key metrics such as **TVL**, **24h Volume**, **24h Fees**, and **APR** for available pools. These help you evaluate the pool’s health and potential earnings.

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### 2. Create a Position

Click **‘Create Position’** to begin the process.

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

### 3. Choose a liquidity provisioning mode

Choose "Manual" to proceed with adding liquidity manually.

### 4. Set Your Price Range & Deposit Amounts

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

#### 4.1 Define the Range

Use available presets or manually set the **min and max prices** of your position. This determines where your liquidity will be active.

* **Narrower ranges** concentrate liquidity, increase potential yield, but require more attention.
* **Wider ranges** reduce the need for active management but dilute fee-earning potential.

> 🔢 Note: Prices are aligned with ticks—predefined intervals—so manual entries will round to the nearest valid tick.

When setting a range, consider:

* Your market expectations (bullish, bearish, sideways)
* How often you can monitor or adjust your position
* Potential network fees or transaction costs

#### 4.2 Input Amounts

Once your range is set, enter the amount of one token; the other token’s amount will autofill based on your price range and the current market price.

* If your range is heavily skewed above or below the current price, you'll provide more of one asset.
* You may also choose to provide **single-sided liquidity** (just one asset), enabling advanced strategies like buy/sell ranges or passive DCA.

> ✅ The “Full Range” option mimics V2-style AMMs and distributes liquidity across the entire price spectrum.

### 5. Confirm & Submit the Transaction

Click **‘Create Position’** and approve the transaction in your connected wallet.

Once confirmed:

* You’ll receive an **on-screen success notification**
* Your assets are now part of the pool and begin **earning fees immediately**

To learn how to manage your position—adjust ranges, claim fees, or withdraw liquidity—continue to the **“Managing & Adjusting Positions”** section of this guide.
