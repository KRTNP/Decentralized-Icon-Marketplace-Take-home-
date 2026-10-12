# Decentralized Icon Marketplace (take-home)

A dApp built for a take-home assignment. Sellers list icons for sale in ETH, and buyers pay on-chain to unlock the download link.

## Structure

- `backend/`: Hardhat, the Solidity contract and its tests, and the API server
- `frontend/`: the dApp UI, built with Vite and vanilla JS

## Running locally

You need four terminals.

**1. Start a local blockchain**

```bash
cd backend
npm install
npx hardhat node
```

**2. Deploy the contract**

```bash
cd backend
npx hardhat run scripts/deploy.js --network localhost
```

Copy the deployed address into `DEPLOYED_CONTRACT_ADDRESS` in `backend/.env` and `VITE_DEFAULT_CONTRACT_ADDRESS` in `frontend/.env`.

**3. Start the API server**

```bash
cd backend
npm run api
```

**4. Start the frontend**

```bash
cd frontend
npm install
npm run dev
```

Then open `http://127.0.0.1:8080`.

## Seller flow

To add an icon, the seller enters an `Icon Name` and a `Price (ETH)`, then provides two files:

- A preview image: either `Use URL` with a custom URL, or `Upload File`, in which case the backend creates a `/uploads/previews/...` URL.
- The downloadable asset: either `Use URL` with a custom URL, or `Upload File`, in which case the backend creates a `/uploads/assets/...` URL.

## Buyer flow

1. The buyer clicks `Buy`.
2. The buyer clicks `Reveal URL`.
3. The frontend opens `/download.html?iconId=...`.
4. `download.html` requests a nonce and signature, calls `/api/reveal-url`, and shows the download link.

If the asset was uploaded to the backend (`/uploads/assets/...`), the link is a signed temporary URL (`/api/download-asset/...`) that expires after `DOWNLOAD_URL_TTL_SECONDS`.

## Environment variables

### backend/.env

```env
LOCAL_RPC_URL=http://127.0.0.1:8545
DEPLOYED_CONTRACT_ADDRESS=
ICON_URL_SECRET=replace-with-long-secret
API_PORT=4315
API_HOST=127.0.0.1
API_PUBLIC_BASE_URL=http://127.0.0.1:4315
PREVIEW_UPLOAD_MAX_BYTES=5242880
ASSET_UPLOAD_MAX_BYTES=20971520
DOWNLOAD_URL_TTL_SECONDS=300
```

### frontend/.env

```env
VITE_DEFAULT_CONTRACT_ADDRESS=
```

## Tests and build

```bash
cd backend
npx hardhat test

cd ../frontend
npm run test
npm run build
```

## Troubleshooting

| Problem | Fix |
|---|---|
| `Wrong network (chainId ...)` | Click `Switch to Localhost`, or switch MetaMask to chain `31337` manually. |
| `ERR_CONNECTION_REFUSED` on `/uploads/...` | The API server isn't running. Start it with `npm run api` in `backend`. |
| `You already purchased this item` | Expected. The same wallet can't buy the same icon twice. |
| Fallback image shown instead of the preview | The preview URL or file is missing or unreachable. Upload the preview again in the seller form. |

## Release checklist

1. `npx hardhat test` passes.
2. `npm run test` and `npm run build` pass in `frontend`.
3. `.env.example` lists every required variable.
4. `.gitignore` excludes `.env` and `backend/uploads/`.
5. The full flow works locally: add an item, buy it, reveal the URL, and download.
