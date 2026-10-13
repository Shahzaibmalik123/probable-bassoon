# DataPilot AI Data Agent — Vercel deployment (Roman Urdu)

Is build mein SQLite aur local disk uploads hata diye gaye hain. Persistent data ke liye Supabase Postgres aur asal uploaded files ke liye private Supabase Storage bucket use hota hai. Vercel Express app ko serverless function ke taur par run karta hai.

## Free services
- **Vercel Hobby**: website/backend hosting ke liye free tier, personal/hobby use ke liye.
- **Supabase Free**: is waqt Free plan mein 500 MB database size aur 1 GB file storage quota hai. Free projects inactivity par pause ho sakte hain aur free tier par automatic backups ki guarantee nahi hoti. Current quotas badal sakte hain; deploy se pehle official pricing check karein.
- **OpenAI API**: optional hai aur aam tor par API usage paid hoti hai. API key na ho to keyword-based basic mode chalega.

## A. Supabase setup
1. https://supabase.com par account banayein aur New project create karein.
2. Database password ko safe jagah save karein.
3. Project dashboard mein **SQL Editor → New query** kholein.
4. Is folder ki `supabase-schema.sql` file ka poora content paste karke **Run** dabayein.
5. **Storage** page par confirm karein ke `agent-files` bucket maujood hai aur **Private** hai.
6. **Project Settings → API** (ya Connect/API keys page) se Project URL aur `service_role` secret key copy karein. Service-role key secret hai: GitHub, screenshot, public/app.js ya kisi public jagah par share na karein.

## B. GitHub upload
1. ZIP extract karein.
2. Extracted `ai_data_agent_vercel` folder ke andar ki files repository ke root mein upload karein (folder ko nested folder ki surat mein upload na karein).
3. Confirm karein ke `server.js`, `package.json`, `vercel.json`, `supabase-schema.sql`, aur `public/` repository root par hain.
4. `.env` ya real keys GitHub par upload NA karein.

## C. Vercel deploy
1. https://vercel.com/new par jayein aur GitHub repo Import karein.
2. Root Directory repository root rakhein.
3. Framework preset ko Other/No framework chhor sakte hain. Build command aur output directory ki zaroorat nahi.
4. **Environment Variables** mein ye variables add karein:
   - `SUPABASE_URL` = Supabase Project URL
   - `SUPABASE_SERVICE_ROLE_KEY` = Supabase service_role secret key
   - `SUPABASE_STORAGE_BUCKET` = `agent-files`
   - `MAX_FILE_MB` = `4`
   - `OPENAI_API_KEY` = optional; apni OpenAI key (blank/omit kar sakte hain)
   - `OPENAI_MODEL` = `gpt-4o-mini` (optional)
5. Deploy dabayein aur deployment complete hone ka wait karein.
6. Apni Vercel domain par `/api/health` kholein. `ok: true`, `storageConfigured: true` aur Supabase storage status nazar aana chahiye.
7. CSV ya TXT file upload karke test karein; phir page reload karein. File list rehni chahiye, kyun ke metadata Postgres mein aur original file Supabase Storage mein save hoti hai.

## D. Local testing (optional)
Node.js 20+ install karein. Project folder mein terminal khol kar:

```bash
npm install
```

`.env.example` ki copy bana kar `.env` naam dein aur Supabase values fill karein. Phir:

```bash
npm start
```

Browser mein http://localhost:3000 kholein.

## Zaroori security note
Is starter build mein user accounts/login nahi hain; is liye app ko public karne par visitor API endpoints se shared files ko dekh, upload ya delete kar sakta hai. Sirf apne/private test ke liye use karein jab tak authentication aur per-user data isolation add na ho. Service-role key ko kabhi frontend mein na rakhein.

## Free tier ki limitations
- Supabase Free ki current limits aur inactivity pause policy official page par check karein: https://supabase.com/pricing
- Vercel ki current Hobby limits/acceptable use check karein: https://vercel.com/pricing
- Supabase Free mein automated backups nahi hote; important data ki separate backup rakhein.
- AI API usage OpenAI ke charges la sakti hai: https://openai.com/api/pricing/


## Upload limit (important for Vercel)
Vercel Functions request bodies have a size limit. Is build mein `MAX_FILE_MB=4` rakha gaya hai taa-ke multipart upload request Vercel ki request limit ke andar rahe. Agar aapko bari files chahiyein, to browser se Supabase Storage par direct signed upload implement karna hoga; sirf server limit barhane se kaam nahi chalega.

## Vercel framework
`vercel.json` mein Express framework set hai. Vercel dashboard mein Build Command/Output Directory manually set karne ki zaroorat nahi honi chahiye; agar dashboard koi custom purani setting dikhaye to default/automatic settings restore karein.
