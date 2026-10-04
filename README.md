# Perfect Computer — Website (Netlify-ready)

## इसमें क्या जोड़ा गया है
- **Favicon**: अब आपके `logo_ultra_smooth.svg` से बना है (`assets/logo.svg`), नेव बार में logo भी यही है।
- **Login के बाद अलग page**: Login सफल होते ही एक अलग "Student Portal" page खुलता है (marketing site छिप जाती है)।
- **Hamburger (☰) menu**: सिर्फ login के बाद ऊपर बाईं तरफ दिखता है। Tap करने पर एक shutter जैसा popup नीचे खुलता है जिसमें:
  - 👤 **Profile** — वापस उसी profile page पर।
  - 📚 **Study Material** — दो categories: **RSCIT** और **Tally**।
- हर category में उसकी PDF files topic के नाम से दिखती हैं (जैसे "RSCIT — Fundamental", "Tally — Gateway of Tally")।
- **PDF Viewer**: PDF सीधे download link या iframe से नहीं खुलती — हर page को canvas पर render किया जाता है, इसलिए सामान्य "Save As" / drag-out काम नहीं करता। Viewer के अंदर right-click, Ctrl+P, Ctrl+S, Ctrl+U, F12, DevTools shortcuts block किए गए हैं।
- **Screenshot रोकने की कोशिश (सीमाओं के साथ)**:
  1. हर page पर student का नाम + Registration No. + समय watermark के रूप में सीधे PDF page पर ही बेक (bake) कर दिया जाता है — अगर कोई copy लीक भी होती है तो पता चल जाएगा किस student के login से हुई।
  2. Tab/window से focus हटते ही (Alt+Tab, दूसरा app खोलना, आदि) content तुरंत धुंधला (blur) हो जाता है और वापस आने पर ही दिखता है।
  3. Windows के "PrintScreen" key दबाने पर तुरंत clipboard खाली करने की कोशिश की जाती है ताकि paste करने पर image खाली आए।
  - ⚠️ **पूरी तरह ईमानदार बात**: कोई भी website असली OS-level screenshot, screen-recording software, या दूसरे फ़ोन से खींची गई फ़ोटो को रोक नहीं सकती — यह browser की सीमा से बाहर की चीज़ है। ऊपर के तरीके सिर्फ आसान/casual screenshot को मुश्किल बनाते हैं और leak होने पर traceable बनाते हैं (watermark की वजह से), पूरी तरह असंभव नहीं बनाते।
- **Fullscreen / Landscape / Portrait**: PDF viewer के अंदर बटन दिए हैं। Orientation lock सिर्फ mobile browsers में और सिर्फ fullscreen mode में काम करता है — यह browser/OS की सीमा है, वेबसाइट की नहीं।
- **Firebase**: आपकी पुरानी site में पहले से Firebase Firestore जुड़ा हुआ था (project: `perfectcomputer-1ece6`) — registration पहले से वहीं save हो रहा था। अब login के समय भी `lastLogin` और `loginHistory` फ़ील्ड उसी student के document में save होते हैं।

## Firebase में आपको कुछ नया setup नहीं करना
Firebase पहले से configured है और register होने वाला हर student `students` collection में अपने Registration No. के document में पूरी details के साथ save होता है — नाम, पिता/माता का नाम, आधार, मोबाइल, DOB, gender, courses, password, आदि। यह पहले से काम कर रहा था; मैंने सिर्फ हर login पर `lastLogin` टाइमस्टैंप जोड़ने का काम बढ़ाया है।

**ज़रूरी सुरक्षा जांच:** Firebase Console → Firestore Database → Rules में जाकर देख लें कि पढ़ने-लिखने की अनुमति सिर्फ ज़रूरी हद तक खुली हो। अभी login/registration दोनों client-side से सीधे Firestore को query करते हैं (कोई Firebase Authentication काम में नहीं ली गई), इसलिए अगर आपके rules पूरी तरह खुले हैं तो technically कोई भी छात्रों का data पढ़ सकता है अगर उसे आपका Firebase config पता चल जाए (जो कि इस website की source में वैसे भी दिखता है)। भविष्य में ज़्यादा सुरक्षा चाहिए तो Firebase Authentication की तरफ जाना सही रहेगा — फ़िलहाल के लिए यह वैसा ही है जैसे आपकी पुरानी site पहले से काम कर रही थी।

## Admin Panel
अब कोई अलग `admin.html` page नहीं है — same **Login** बटन और वही login box इस्तेमाल होता है। जब कोई इन details से login करता है:

- **Username field में:** `admin`
- **Password field में:** `Gogunda@8890714911`

...तो student profile की जगह **Admin Panel** खुल जाता है — सभी students की list (नाम, मोबाइल, courses, registration date, last login), साथ में search box और stats।

⚠️ यह login भी सिर्फ एक client-side check है (आपकी मौजूदा student-login जैसी ही तकनीक) — असली मज़बूत सुरक्षा नहीं है, सिर्फ आम visitors को बाहर रखता है। पासवर्ड `index.html` फ़ाइल में साफ़ लिखा दिखता है अगर कोई source code खोलकर देखे। पासवर्ड बदलना हो तो `index.html` में `ADMIN_PASSWORD` वाली line ढूंढकर बदल दें (Ctrl+F से खोजें)।

## Study Material में नई PDF कैसे जोड़ें
1. PDF फ़ाइल को `assets/pdfs/rscit/` या `assets/pdfs/tally/` folder में डालें।
2. `index.html` खोलें, `STUDY_MATERIAL` नाम का JavaScript object ढूंढें (Ctrl+F से खोजें), और एक नई line जोड़ें जैसे:
   ```js
   { title: 'नया Topic नाम', file: 'assets/pdfs/rscit/FileName.pdf' }
   ```

## Netlify पर Deploy कैसे करें
1. इस zip को unzip करें।
2. [netlify.com](https://app.netlify.com) पर जाएं → **Add new site → Deploy manually (drag & drop)**।
3. पूरे unzip किए हुए folder को drag करके drop कर दें (या "Browse" से चुनें)।
4. कुछ ही seconds में आपकी site live हो जाएगी और एक Netlify URL मिलेगा।
5. बाद में अपना खुद का domain भी Netlify के **Domain settings** से जोड़ सकते हैं।

Folder structure:
```
/
├── index.html
├── netlify.toml
└── assets/
    ├── logo.svg
    └── pdfs/
        ├── rscit/  (5 PDFs)
        └── tally/  (4 PDFs)
```
