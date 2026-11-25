ADVOCATE DIARY — FULL GROUP UPDATE + SERVER TRASH BUILD
Firebase project: advocate-diary-366f8

PACKAGE CONTENTS
- index.html, master.html, admin.html, user.html
- Firestore rules and collection-group index definition
- Firebase Cloud Functions (case update email, audit log, Trash expiry, permanent delete, scheduled purge)
- PWA manifests, icons, service worker

FRONTEND CHANGES
1. Dashboard name button now shows the logged-in profile name and User ID.
2. New “अपने Group के Updates” action opens a dropdown-driven group update viewer.
   - Master: own Master group plus Admin groups listed in the Master account list.
   - Admin: own Admin group only.
   - User: own parent Admin group only.
3. Update history shows case number, parties, court/room, note, next date, updater name/role and clickable email address.
4. Master gets “Server Dustbin”, group selector, retention-days control (default 30), and Permanent Delete action.
5. Permanent Delete is executed through a callable Cloud Function which verifies the active Master and checks that the selected team belongs to that Master.

SERVER FUNCTIONS
- emailGroupOnCaseUpdate: creates a team-scoped audit record and emails active members of that same group using BCC.
- stampTrashExpiry: stamps purgeAt based on trashSettings/{teamId}.retentionDays; default 30 days.
- permanentDeleteCase: Master-only callable function; only deletes a case already marked del=true.
- purgeExpiredTrash: scheduled daily purge of expired Trash cases.

IMPORTANT DEPLOYMENT STEPS
A. Upload the four HTML files, manifests, icons and sw.js to the existing GitHub Pages repository root.
B. From a computer with Node.js 20 and Firebase CLI installed, extract this ZIP and run:
   npm install -g firebase-tools
   firebase login
   firebase use advocate-diary-366f8
   cd functions
   npm install
   cd ..
C. Configure SMTP secrets (use provider SMTP/app-password credentials, never put them in HTML or GitHub):
   firebase functions:secrets:set SMTP_HOST
   firebase functions:secrets:set SMTP_PORT
   firebase functions:secrets:set SMTP_USER
   firebase functions:secrets:set SMTP_PASS
   firebase functions:secrets:set MAIL_FROM
D. Deploy backend and Firestore rules/indexes:
   firebase deploy --only functions,firestore:rules,firestore:indexes

BILLING / PROVIDER REQUIREMENTS
- Cloud Functions and scheduled functions may require the Firebase project to be on the Blaze billing plan.
- SMTP credentials must be from a real mail provider; email delivery is not active until secrets are set and Functions are deployed.
- Google/Gmail normal account password must not be used. Use a provider app password or SMTP credential stored as Firebase Secret.

DATA / COMPATIBILITY
- Existing shared team case documents at teams/{teamId}/cases/{ownerUid}__{caseId} are used for server email and Trash.
- Legacy compressed userData chunks remain as the app's local/backup sync format; this build does not automatically migrate or erase those chunks.
- Team update viewer reads teamUpdates/{teamId}/items written by Cloud Functions after deployment. It will show new updates going forward; it does not reconstruct historic updates.
- Existing Trash documents already marked del=true may not have purgeAt; the retention trigger stamps only when a case newly enters Trash. Restore and delete again to start a new retention period.

VALIDATION STATUS
- JavaScript syntax checked for index/master/admin/user and Functions source.
- ZIP integrity checked.
- No live Firebase deployment, SMTP send, callable execution, scheduled purge, or Firestore Rules Emulator test was possible here. Do not interpret local syntax checks as live-server certification.
- Review Firestore Rules in Firebase Emulator/Rules Playground before publishing to production. Keep a backup/export before deploying rules.


पुराना प्रति-अपडेट checkbox अब उपयोग में नहीं है। ईमेल भेजने/रोकने का नियंत्रण केवल Master के Group-wise Email Authorization में है।


नई सुविधा: Master Dashboard में “Group Email Authorization” बटन से Master अपने Group और प्रत्येक सीधे जुड़े Admin Group का Automatic Email अलग-अलग ON/OFF कर सकता है। यह सेटिंग Firestore के emailSettings/{teamId} में सुरक्षित होती है। जिस Group का विकल्प ON है, उसमें Master, Admin या कोई भी User केस अपडेट करे तो उसी Group के सभी Active Members को ईमेल जाएगा। OFF होने पर उस Group में किसी भी भूमिका के सदस्य के अपडेट पर ईमेल नहीं जाएगा। दूसरे Groups अप्रभावित रहेंगे। इसे लागू करने के लिए नई Firestore Rules और Cloud Functions भी deploy करें।

अतिरिक्त अपडेट:
- Group Updates स्क्रीन पर Previous Date और Next Date अब dd-mmm-yyyy (जैसे 05-Nov-2026) में दिखेंगी।
- अपडेट करने वाले के नाम पर क्लिक करने पर उसकी Email ID दिखाई देगी।
- ईमेल सूचना में भी Previous Date और Next Date dd-mmm-yyyy प्रारूप में भेजी जाएंगी।
