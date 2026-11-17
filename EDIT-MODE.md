# Paano mag-edit ng BUONG invitation (`?edit`)

1. Buksan ang site na may `?edit` sa dulo ng link, hal. `https://yoursite.com/index.html?edit`
2. Pindutin ang 🎨 sa kanang-ibaba at ilagay ang passcode (default: `keziah2026`).
   Palitan ito: hanapin ang `EDIT_PASS` sa `index.html`.
3. May 5 tabs:

| Tab | Ano ang mae-edit | Save |
|---|---|---|
| 📝 **Details** | Pamagat, **petsa at oras (countdown)**, simbahan + oras, reception + oras, attire, **address at Google Maps link**, RSVP deadline, mga text sa Gallery/Sponsors/Gifts, Thank You, pirma ng pamilya | pindutin ang **💾 I-save** |
| 🖼 **Photos** | **Cover picture ng main page** (papalit sa video), **baby photo**, **8 gallery photos** | auto-save |
| 👪 **Ninong/Ninang** | Listahan ng mga Ninong at Ninang (isang pangalan bawat linya) | pindutin ang **💾 I-save** |
| 🎁 **Gifts** | Brands (add/remove), logo ng bawat brand, gift ideas | pindutin ang **💾 I-save** (logo: auto-save) |
| 🎨 **Backgrounds** | Background ng bawat section (Fill / Fit height / ✕ ibalik sa original) | auto-save |

Mga tip:
- **Petsa/oras:** kapag binago ang "Petsa at oras ng ceremony", awtomatikong magbabago ang countdown, ang "Petsa na ipapakita" (pati sa ilalim ng cover), at ang "Oras ng ceremony".
- **Maps:** i-paste ang Google Maps *Share link* sa "Google Maps link". Kung walang link, gagawa ang site ng directions link mula sa address.
- **Cover picture:** kapag may na-upload, ito ang lalabas sa main page imbes na video. Pindutin ang ✕ para ibalik ang video.
- Kapag blanko ang isang text field, ibabalik sa orihinal na text.
- Awtomatikong pinaliliit ang mga image bago i-save (para pasok sa limit ng Firestore).
- Hindi mae-edit dito: ang video (`hero-video.mp4`) at ang lullaby (`lullaby-music.mp3`) — palitan ang mga file na iyon mismo.

## One-time setup: Firestore rules
Firebase Console -> Firestore Database -> Rules. Idagdag ito sa loob ng `match /databases/{database}/documents { ... }`
(**kailangan na ang pangalawang block (`siteContent`) para ma-save ang mga bagong edits**):

```
match /siteBackgrounds/{id} {
  allow read: if true;
  allow write: if id in ['gate','hero','countdown','gallery','details','location','sponsors','gifts','rsvp','guestbook','thankyou']
    && request.resource.data.keys().hasOnly(['img','fit','color','updatedAt'])
    && (request.resource.data.img == null
        || (request.resource.data.img is string && request.resource.data.img.size() < 950000));
}

match /siteContent/{id} {
  allow read: if true;
  allow write: if (id == 'main'
        && request.resource.data.keys().hasOnly(['data','updatedAt'])
        && request.resource.data.data is map)
    || (id.matches('img_[a-z0-9_]+')
        && request.resource.data.keys().hasOnly(['img','updatedAt'])
        && (request.resource.data.img == null
            || (request.resource.data.img is string && request.resource.data.img.size() < 950000)));
}
```

## Mga paalala
- Ang passcode ay panakip lang laban sa aksidente (nakikita ito sa source code). Hindi ito totoong seguridad — ang rules sa itaas ay bukas para sa sinumang makakaalam ng Firestore config, kaya ang pinakaligtas na paraan ay huwag ibahagi ang `?edit` link. (Ang lahat ng text na galing sa Firestore ay ligtas na ipinapakita bilang plain text.)
- **Ninong/Ninang:** ang "Responded" ng bawat pangalan ay naka-link sa mismong pangalan. Kapag pinalitan ang pangalan, bagong response slot ito (nananatili pa rin sa Firestore ang lumang response).
