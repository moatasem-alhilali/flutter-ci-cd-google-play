
# 🚀 Flutter Google Play Deployment Automation with Fastlane & GitHub Actions

A complete example of how to **automate Flutter app deployment to Google Play** using [Fastlane](https://fastlane.tools/) and [GitHub Actions](https://docs.github.com/en/actions), with production-safe credentials and real workflow files.

---

## 📦 What’s Inside

- **Fastlane configuration** for automated Android releases
- **GitHub Actions workflow** for continuous integration and deployment (CI/CD)
- Example tracks: Internal, Alpha, Beta, Production
- Version bumping and code signing automation

---

## 🌟 Why This Repo?

- **Save hours** of manual Play Store uploads.
- Minimize human error and ensure consistent releases.
- Production-ready setup you can clone and adapt in minutes.

---

## 🛠️ Prerequisites

- A working Flutter project
- Access to [Google Play Console](https://play.google.com/console/)
- A Google Cloud **Service Account** with access to your Play Console project (see below)
- A private signing keystore for your app

---

## ⚡ Quick Start

### 1. Clone This Repo or Copy the Fastlane Files

You only need the `android/fastlane` folder and `.github/workflows` for automation.

### 2. Create a Google Play Service Account

- Go to [Google Cloud Console](https://console.cloud.google.com/).
- Create a **Service Account**.
- Grant it the **Release Manager** role on your Play Console app (add the generated email to Users & Permissions).
- Generate a JSON key file (e.g., `play-store-service-account.json`).
- **Never commit this file!**  
  Instead, store its contents as a GitHub Actions Secret named `GOOGLE_PLAY_JSON`.

### 3. Fastlane Setup

- In your Flutter project, run:
  ```bash
  cd android
  fastlane init
````

* Place your service account JSON at:

  ```
  android/fastlane/keys/play-store-service-account.json
  ```
* Configure `android/fastlane/Appfile` and `Fastfile` as in this repo (edit package name as needed).

### 4. Configure Signing Keys

* Place your app signing keystore in a secure location.
* Store all passwords as GitHub Secrets; never commit secrets to the repo.

### 5. GitHub Actions Workflow

* Example workflows for each deployment track are in `.github/workflows/`.
* On push to a branch (e.g., `production`), the workflow will:

  1. Checkout the repo
  2. Set up Ruby, Flutter, and dependencies
  3. Decode your Play service account key from Secrets
  4. Run Fastlane to build and upload to Google Play

### 6. Branch-to-Track Mapping

| Branch       | Google Play Track |
| ------------ | ----------------- |
| `internal`   | Internal          |
| `alpha`      | Alpha             |
| `beta`       | Beta              |
| `production` | Production        |

---

## 🔐 Security Best Practices

* **Never commit service account keys or keystores.**
  Use [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) for all sensitive information.
* Grant your service account only the minimum permissions required.
* Always review build and deployment logs for errors.

---

## 📈 Workflow Example

```yaml
name: Deploy to Production

on:
  push:
    branches:
      - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.2'
      - name: Set up Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.19.6'
      - name: Install Flutter dependencies
        run: flutter pub get
      - name: Install Bundler & Fastlane
        working-directory: android
        run: |
          gem install bundler
          bundle install
      - name: Decode service account key
        run: |
          mkdir -p android/fastlane/keys
          echo "${{ secrets.GOOGLE_PLAY_JSON }}" | base64 --decode > android/fastlane/keys/play-store-service-account.json
      - name: Deploy to Production
        working-directory: android
        run: bundle exec fastlane deploy_production
```

---

## 📝 Notes

* **First Release:** You must manually upload your app once in the Play Console before using Fastlane automation.
* **Track Selection:** To deploy to different tracks, just push to the corresponding branch.
* **Version Codes:** The provided Fastlane config automatically bumps your version code based on the latest published version in Google Play.

---

## 📚 References

* [Fastlane: Play Store Deployment](https://docs.fastlane.tools/getting-started/android/play-store/)
* [GitHub Actions: Flutter Action](https://github.com/subosito/flutter-action)
* [Google Play Console: Users & Permissions](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en)

---

## 🤝 Contributing

Feel free to open issues or pull requests if you have improvements or questions!

---

## 📣 Author

[Your Name](https://www.linkedin.com/in/yourprofile/) — Senior Full Stack & Mobile Engineer

---



