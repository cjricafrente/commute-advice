# Rain or Shine: Commute Advice 🌦️

A lightweight serverless web app that turns live weather forecasts into a clear decision: **LEAVE now** or **WAIT**.  
It combines live external data, on-demand compute, managed storage, and a static web interface into one cohesive system.

👉 Demo: https://<your-username>.github.io/commute-advice/

## Highlights
- **Live data ingestion** (Open-Meteo) on demand
- **On-the-fly processing** in AWS Lambda with configurable look-ahead/threshold
- **Cache with TTL** in DynamoDB to avoid unnecessary recompute
- **Zero-maintenance frontend** on GitHub Pages (HTTPS, GPS-ready)
- **Cost-aware**: stays within AWS always-free usage

## How it fits together
1. The web page (GitHub Pages) optionally gets GPS and calls the **Lambda Function URL**.
2. Lambda checks **DynamoDB** for a fresh result; if stale, it fetches a new forecast, computes advice, stores it, and returns JSON.
3. The page renders **LEAVE/WAIT** plus a mini chart of upcoming rain probabilities.

## Repo contents
- `index.html` — static UI (mobile-friendly, GPS + mini chart)
- `lambda/handler.py` — Python Lambda (Function URL) for fetch → compute → cache → serve
- `docs/architecture.md` — ASCII diagram of the flow

## Deploy (short)
- **DynamoDB**: table `commute_summary` with PK=`pk` (String), SK=`sk` (String); enable TTL on `ttl_epoch`.
- **Lambda**: create function with `lambda/handler.py`; set env vars (`CITY_KEY`, `CITY_NAME`, `LAT`, `LON`, `SUMMARY_TABLE`, `AHEAD_HOURS`, `THRESHOLD`, `STALE_MINUTES`, `KEEP_DAYS`); IAM policy: `dynamodb:Query`, `dynamodb:PutItem` on `commute_summary`; create **Function URL** (Auth: None; leave CORS OFF).
- **Frontend**: set `const API = "<your Function URL>/"` inside `index.html`; push to GitHub; enable **GitHub Pages** (Branch: `main`, Folder: `/`).

## Cost
- **Lambda**: ~1 request per page load; ≪ 1M free reqs/mo.
- **DynamoDB**: tiny reads/writes; ≪ 200M free ops + 25GB storage.
- **GitHub Pages**: free.
- **Logs**: ≪ 5GB/mo free.
Result: effectively **₱0/month**.

## Data source
- **Open-Meteo** hourly precipitation probability (JSON).

## Notes
- Try `?threshold=70&ahead=4` on the URL.
- Button **Use my location** requests GPS (works because Pages is HTTPS).
