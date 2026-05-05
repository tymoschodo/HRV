# System Nerwowy — HRV Dashboard

Aplikacja do śledzenia HRV i stanu układu nerwowego. Działa w przeglądarce, dane w Firebase Firestore, bez backendu.

## Setup (10-15 minut)

### 1. Firebase

1. Wejdź na [console.firebase.google.com](https://console.firebase.google.com)
2. Wybierz swój projekt (lub utwórz nowy)
3. **Authentication** → Sign-in method → Google → Enable
4. **Firestore Database** → Create database → Production mode → wybierz region
5. **Firestore → Rules** → wklej poniższe i kliknij Publish:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/sessions/{sessionId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

6. **Project Settings** (ikona ⚙) → Your apps → Add app → Web → zarejestruj
7. Skopiuj obiekt `firebaseConfig` (będzie potrzebny w aplikacji)

### 2. GitHub Pages

```bash
# Utwórz repo na GitHubie, np. "hrv-dashboard"
git init
git add index.html README.md
git commit -m "init"
git remote add origin https://github.com/TWOJ_USERNAME/hrv-dashboard.git
git push -u origin main
```

Potem: repo → Settings → Pages → Source: Deploy from branch → main → / (root) → Save

Aplikacja będzie pod: `https://TWOJ_USERNAME.github.io/hrv-dashboard`

### 3. Autoryzuj domenę w Firebase

Firebase Console → Authentication → Settings → Authorized domains → Add domain:
```
TWOJ_USERNAME.github.io
```

### 4. Pierwszy launch

1. Otwórz aplikację
2. Kliknij **⚙ Konfiguracja**
3. Wklej JSON z firebaseConfig
4. (Opcjonalnie) Wklej klucz Anthropic z [console.anthropic.com](https://console.anthropic.com) — do generowania interpretacji tekstowych przez AI. Bez klucza aplikacja używa gotowych reguł.
5. Zapisz → zaloguj się Google → gotowe

---

## Używanie

1. Zrób pomiar w swojej aplikacji HRV
2. Eksportuj 4 pliki: `Features_.csv`, `HR_.csv`, `RR_.csv`, `RR_KUBIOS_.txt`
3. Kliknij **+ Wgraj sesję** i zaznacz wszystkie 4 pliki naraz w Finderze
4. Gotowe — dane zapisują się do Firestore

## Co aplikacja mierzy

| Wskaźnik | Co oznacza |
|----------|-----------|
| **rMSSD** | Główny wskaźnik aktywności przywspółczulnej. Wyższy = lepsza regeneracja |
| **SDNN** | Ogólna zmienność rytmu serca |
| **LF/HF** | Balans współczulny/przywspółczulny. <1 = relaks, >2 = stres |
| **DFA alpha1** | Fraktalna dynamika. ~1.0 = elastyczny układ, >1.5 = obciążenie |
| **vs baseline** | Porównanie do Twojego osobistego średniego z ostatnich 7 dni |

## Uwaga o baseline

Aplikacja porównuje Twoje dane do **Twojego osobistego baseline** (rolling 7-day average), nie do tabel populacyjnych. Pierwsze 3 sesje to "rozruch" — od 4. sesji zaczynają pojawiać się delty procentowe.
