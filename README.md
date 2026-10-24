# Rehnuma

An open source WhatsApp health guide on menstruation, puberty and postpartum care for girls and women in Pakistan, in Urdu, Roman Urdu and English, by text or voice note.

Built by Swift Hygiene Industry (Pvt.) Ltd., Karachi. Contact: info@swift-industries.com

## Why

Menstrual and postpartum health information in Pakistan is surrounded by stigma and myth, and many women and girls have no private, trustworthy place to ask. Most femtech apps need a smartphone app download, English literacy and data. Rehnuma works inside WhatsApp, accepts voice notes, answers in the language the user writes in, and is distributed through a QR code on every Kamfit and Neatex pack and at partner pharmacies.

## What it does

* **Answers questions** in the user's own words, matched to a knowledge base where every answer is reviewed by a doctor. The bot never invents medical advice.
* **Safety first.** Every message is checked for danger signs (bleeding in pregnancy, very heavy bleeding, fainting, danger signs after delivery, thoughts of self harm) before anything else. These get an immediate referral to a hospital, 1122, Edhi 115, or the Umang helpline.
* **Cycle tracker.** Send PERIOD when a period starts and NEXT to see an estimate. Irregular cycles prompt a doctor visit.
* **Voice notes** transcribed with open source Whisper, for users who cannot read or type comfortably.
* **Needs map.** Anonymised, aggregated data on what women ask about, by district, plus a backlog of unanswered questions. Useful for health departments, Lady Health Worker programmes and partners.

## Privacy by design

* Phone numbers are never stored. Users are identified by a salted HMAC code.
* Explicit consent before any data is recorded. Location (district) is optional.
* Unanswered questions are stored with digits removed and no link to any user.
* Reports merge districts with fewer than 5 users into "Other".
* Users can type DELETE at any time to erase all their data.

## Project layout

```
kb/knowledge_base.json     doctor reviewed Q&A in en / ru (Roman Urdu) / ur
rehnuma/bot.py              conversation logic (onboarding, menu, answers, cycle tracker)
rehnuma/safety.py           red flag detection, runs before everything else
rehnuma/kb.py               multilingual retrieval (character n gram TF IDF)
rehnuma/language.py         Urdu script / Roman Urdu / English detection
rehnuma/store.py            SQLite storage with hashed identities
rehnuma/voice.py            Whisper transcription (optional)
rehnuma/app.py              WhatsApp Cloud API webhook (FastAPI)
rehnuma/simulate.py         chat in the terminal without WhatsApp
tools/export_review_sheet.py   knowledge base to Excel for medical review
tools/import_review.py         doctor's decisions back into the knowledge base
tools/needs_map.py             pilot summary and needs by district
tests/                         automated tests
```

## Try it in 1 minute

```
pip install -r requirements.txt
python -m rehnuma.simulate --demo     # scripted conversation
python -m rehnuma.simulate            # chat yourself
python -m pytest -q                  # run the tests
```

## Medical review workflow

1. `python tools/export_review_sheet.py` creates the review sheet.
2. The doctor sets Status for every answer and safety message (Approved, Needs change, Remove).
3. `python tools/import_review.py <sheet>` copies decisions into the knowledge base and lists what to fix.
4. In production (`REHNUMA_APPROVED_ONLY=1`, the default) only Approved answers are ever sent.

## Running on WhatsApp

1. Create a Meta developer app with the WhatsApp product and a business phone number.
2. Copy `.env.example` to `.env` and fill in the token, phone number ID, verify token, app secret and a long random `REHNUMA_SALT`.
3. Run `uvicorn rehnuma.app:app --host 0.0.0.0 --port 8000` on any small server with HTTPS.
4. In the Meta dashboard set the webhook URL to `https://<your-domain>/webhook` and subscribe to `messages`.
5. For voice notes: `pip install faster-whisper` (the `small` model runs on a basic CPU server).

## Roadmap

* Multilingual sentence embeddings for better matching of free text questions
* Sindhi and Pashto content
* Lady Health Worker referral directory by district
* Dashboard of the needs map for partners

## License

Code: MIT (see LICENSE). Knowledge base content: Creative Commons Attribution 4.0 (CC BY 4.0).
The Kamfit, Neatex and MOCC™ names and Swift Hygiene's manufacturing technology are not part of this project and are not licensed by it.
