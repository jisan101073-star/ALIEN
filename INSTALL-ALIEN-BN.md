# ALIEN Deployment System — Build v11

এই প্যাকেটে ALIEN-এর নতুন Frontend (`index.html`) এবং Cloudflare Worker (`worker.js`) আছে। Deployment System চালু করতে **Worker আগে**, তারপর Pages Frontend আপডেট করতে হবে।

## প্যাকেটের ফাইল
- `worker.js` — `alien-api` Worker-এর সম্পূর্ণ নতুন কোড
- `index.html` — ALIEN Pages ওয়েবসাইটের সম্পূর্ণ Frontend

## ধাপ ১ — Cloudflare Worker আপডেট
1. Cloudflare Dashboard খুলে **Workers & Pages**-এ যাও।
2. `alien-api` Worker নির্বাচন করো।
3. **Edit code** / Quick Edit খোলো।
4. পুরোনো Worker script-এর পুরো কোডের জায়গায় এই প্যাকেটের `worker.js`-এর সম্পূর্ণ কোড বসাও।
5. **Deploy** চাপো।

**গুরুত্বপূর্ণ:** আগের Settings পরিবর্তন করবে না। `DB` D1 binding, `SUPABASE_URL` variable এবং `SUPABASE_SECRET_KEY` secret আগের মতোই রাখবে। Secret কখনো Frontend বা GitHub-এ বসাবে না।

Worker প্রথমবার `/api/platform-status` বা Deploy API চালালে শুধু নতুন `alien_deployments`, `alien_deployment_files`, `alien_deployment_logs` টেবিল তৈরি করবে। পুরোনো `users`, `projects`, `deployments`, `domains` টেবিল Delete/Reset করবে না। আলাদা SQL migration হাতে চালানোর প্রয়োজন নেই।

### Worker পরীক্ষা
Browser-এ এই ঠিকানাগুলো খুললে JSON আসার কথা:
- `https://alien-api.jisan101073.workers.dev/api/health`
- `https://alien-api.jisan101073.workers.dev/api/storage-health`

প্রথমটিতে Worker ও Database connected এবং দ্বিতীয়টিতে Storage connected থাকা উচিত। `/api/platform-status` প্ল্যাটফর্মের সামগ্রিক অবস্থা দেখায়।

## ধাপ ২ — Cloudflare Pages Frontend আপডেট
1. GitHub-এ `jisan101073-star/ALIEN` repository খোলো।
2. Root-এর `index.html` ফাইল Replace করো এই প্যাকেটের নতুন `index.html` দিয়ে।
3. Commit Changes দাও।
4. Cloudflare Pages-এর `alien` project-এ Deployment Successful হওয়া পর্যন্ত অপেক্ষা করো।
5. `https://alien-2r8.pages.dev` খুলে Sign in করো।

## ধাপ ৩ — প্রথম Website Publish
1. Projects থেকে যে Project Publish করবে সেটি খোলো → **Code**।
2. File Manager-এ ZIP Upload করে **Extract ZIP** চাপতে পারো, অথবা আলাদা HTML/CSS/JS/image ফাইল Upload করতে পারো।
3. `index.html` অবশ্যই Project-এর root-এ থাকতে হবে; ZIP-এর ভিতরে অতিরিক্ত একটি parent folder থাকলেও Extracter একক wrapper folder সরিয়ে নিতে পারে।
4. Preview দেখে নাও এবং **Save files** চাপো।
5. **Deploy website** চাপো। Upload শেষ ও final checks সফল হলে Live URL দেখাবে। Deploy চলাকালে পেজ খোলা রাখবে।

## Deployment সুবিধা
- প্রতিটি Publish নতুন version তৈরি করে।
- Deployment History-তে Successful/Failed version দেখা যায়।
- Build / Deploy Logs-এ upload ও publish ধাপের log থাকে।
- Redeploy সর্বশেষ successful snapshot পুনরায় Publish করে।
- Rollback বেছে নেওয়া পুরোনো successful version-কে আবার Active করে; আগের version history-তে থাকে।
- নতুন version সম্পূর্ণ সফল না হওয়া পর্যন্ত আগের Live version Active থাকে।

## সীমা ও বর্তমান Domain অবস্থা
- প্রতি deployment সর্বোচ্চ 500টি ফাইল ও মোট 45 MiB। Supabase bucket-এর 50 MB default cap-এর নিচে রাখার জন্য এই সীমা রাখা হয়েছে।
- এটি static hosting: HTML, CSS, classic JavaScript, image, font ও অন্যান্য asset। PHP/Node server code বা server-side build command চালায় না।
- Path-based Live URL হবে `https://alien-api.jisan101073.workers.dev/site/PROJECT-SLUG/`।
- Asset URL relative রাখো, যেমন `assets/style.css`; `/assets/style.css`-এর মতো root-absolute path এই path-based URL-এ Project root-এ যাবে না।
- Worker path hosting sandbox-এ চলে। Same-origin browser features (যেমন `localStorage`, service workers বা কিছু JavaScript module imports) সব সাইটে কাজ নাও করতে পারে; per-site isolated hostname/custom-domain routing পরের domain stage-এ প্রয়োজন হবে।
- Custom Domain DNS, wildcard ALIEN subdomain ও custom hostname SSL এখনো configure হয়নি। তাই UI এগুলোকে Pending / Not configured দেখাবে; Live Worker path URL-কে custom domain হিসেবে দেখাবে না।

## সুরক্ষা
- Supabase private bucket-এ ফাইল যায়; Service secret শুধু Worker-এর ভিতরে থাকে।
- Frontend-এ Secret key যোগ করবে না।
- Project Delete, Clear Workspace, D1 Reset বা Storage bucket Delete এই ইনস্টলেশনের অংশ নয়। এগুলো কোরো না।
