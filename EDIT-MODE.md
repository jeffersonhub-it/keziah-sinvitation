# Paano mag-upload ng background per page

1. Buksan ang site na may `?edit` sa dulo ng link, hal. `https://yoursite.com/index.html?edit`
2. Pindutin ang 🎨 sa kanang-ibaba at ilagay ang passcode (default: `keziah2026`).
   Palitan ito: hanapin ang `EDIT_PASS` sa `index.html`.
3. Pindutin ang **Upload** sa section na gusto mo at pumili ng image.
   - **Fill** = pupunuin ang buong section (maaaring may maputol sa gilid).
   - **Fit height** = buong taas ng image ang makikita (para sa patayong images).
   - **✕** = ibalik sa original na background.

## One-time setup: Firestore rules
Firebase Console -> Firestore Database -> Rules. Idagdag ito sa loob ng `match /databases/{database}/documents { ... }`:

```
match /siteBackgrounds/{id} {
  allow read: if true;
  allow write: if id in ['gate','hero','countdown','gallery','details','location','sponsors','gifts','rsvp','guestbook','thankyou']
    && request.resource.data.keys().hasOnly(['img','fit','color','updatedAt'])
    && (request.resource.data.img == null
        || (request.resource.data.img is string && request.resource.data.img.size() < 950000));
}
```
Tandaan: ang passcode ay panakip lang laban sa aksidente (nakikita ito sa source code). Hindi ito totoong seguridad.
