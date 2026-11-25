ADVOCATE DIARY — MASTER / ADMIN SECURITY UPDATE

मुख्य नियम
1. Admin कभी Master नहीं बन सकता। Admin अपने या किसी दूसरे खाते की role को master नहीं कर सकता।
2. Master अपने Master Group में Admin की तरह काम कर सकता है: अपने Group के केस जोड़ना/अपडेट करना, अपने Users को मैनेज करना और अपने Group की सेटिंग नियंत्रित करना।
3. Master केवल अपने सीधे बनाए हुए Admin Groups और उन Admins के Users को मैनेज कर सकता है। किसी दूसरे Master या असंबंधित Admin के Group तक पहुंच Firestore Rules से रोकी गई है।
4. Admin अपने Group और अपने Users तक सीमित है।
5. Admin का Self-Registration बंद है। नया Admin केवल अपने Master द्वारा बनाया जाएगा।
6. पहला Master केवल सत्यापित advocategovindpandey@gmail.com खाते से bootstrap हो सकता है। इसके बाद config/app में दर्ज Master UID के अतिरिक्त कोई दूसरा UID Master profile नहीं बना सकता।
7. केस का Permanent Delete क्लाइंट से बंद है; Master-verified Cloud Function ही इसे कर सकता है।
8. ईमेल Authorization और Trash retention Master के अपने Group तथा उसके सीधे Admin Groups तक सीमित हैं।

DEPLOYMENT
- इस ZIP के advocate_build/firestore.rules को Firebase Console > Firestore Database > Rules में लगाएं।
- Publish करने से पहले वर्तमान Rules की कॉपी/बैकअप रखें।
- Firebase Rules Simulator/Emulator में नीचे के परिदृश्य जाँचें:
  a) Admin अपना role master नहीं कर सकता।
  b) Admin किसी अन्य Admin या Master की profile नहीं बदल सकता।
  c) Master अपने Group में case/user operations कर सकता है।
  d) Master अपने सीधे Admin Groups में काम कर सकता है।
  e) Master किसी दूसरे Master के Admin Group को नहीं पढ़/बदल सकता।
  f) बिना Master की अनुमति कोई नया Admin profile नहीं बना सकता।
  g) Master के Dashboard में केवल उसके अपने Admins और उन Admins के Users सूचीबद्ध हों।
- यदि Master का Firebase profile अभी पहली बार बनाया जा रहा है, तो उसी सत्यापित advocategovindpandey@gmail.com खाते से लॉगिन करें।
- Functions के लिए: firebase deploy --only functions
- Rules और indexes के लिए: firebase deploy --only firestore:rules,firestore:indexes

महत्वपूर्ण सीमा
इस पैकेज की HTML JavaScript syntax जाँच और Cloud Functions Node syntax जाँच की गई है। Firebase Rules Emulator/Simulator, वास्तविक Firebase deployment, SMTP email, callable delete और scheduled purge का live परीक्षण नहीं हुआ है। Publish करने से पहले Rules Simulator में ऊपर के सभी मामलों का परीक्षण करें।
