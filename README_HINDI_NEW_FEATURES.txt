ADVOCATE DIARY — SERVER FEATURES PACKAGE

इस पैकेज में तीनों मौजूदा HTML पैनल और Firebase Cloud Functions backend शामिल हैं।

महत्वपूर्ण:
- Functions को Firebase project advocate-diary-366f8 पर deploy करना होगा।
- ईमेल भेजने के लिए SMTP सेवा/ईमेल provider की credentials Firebase Functions secrets में सेट करनी होंगी। Gmail का सामान्य account password HTML या GitHub में न डालें। Provider का app password या SMTP credential ही secret के रूप में रखें।
- SMTP secrets: SMTP_HOST, SMTP_PORT, SMTP_USER, SMTP_PASS, MAIL_FROM.
- Commands:
  cd functions
  npm install
  firebase functions:secrets:set SMTP_HOST
  firebase functions:secrets:set SMTP_PORT
  firebase functions:secrets:set SMTP_USER
  firebase functions:secrets:set SMTP_PASS
  firebase functions:secrets:set MAIL_FROM
  firebase deploy --only functions
- Functions: emailGroupOnCaseUpdate (teams/{teamId}/cases पर वास्तविक case content बदले तो उसी team के active users को BCC ईमेल), stampTrashExpiry (Trash entry पर configurable purgeAt; default 30 days), permanentDeleteCase (server-side Master-only permanent delete callable), purgeExpiredTrash (daily automatic purge).
- Retention बदलने के लिए Firestore document trashSettings/{teamId} में retentionDays संख्या लिखें। Default 30 days.

सीमा / deployment status:
- यह backend source code है; इस environment से आपके Firebase project पर deploy नहीं किया गया।
- मौजूदा HTML में group dropdown, updater email popup, Trash UI, retention setting UI और Updates viewer को इस नए backend callable/schema से जोड़ने का frontend integration अभी बाकी है।
- मौजूदा team case writes `teams/{teamId}/cases/{ownerUid}__{caseId}` पर होती हैं; backend trigger इसी path पर सुनता है।
- मौजूदा app के compressed userData chunks पुराने storage/backup path हैं। इस backend trigger से legacy chunk updates पर email नहीं जाएगा; email trigger shared team case documents पर है।
- Firestore Rules को बिना Emulator/Rules Playground परीक्षण publish न करें।
- कोई live Firebase sign-in, email delivery या scheduled purge test इस पैकेज में पास होने का दावा नहीं है।
