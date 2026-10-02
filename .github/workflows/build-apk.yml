name: Build Tourbillion APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Get Tourbillion source
        uses: actions/checkout@v4

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v4

      - name: Find Android project and build APK
        shell: bash
        run: |
          set -e

          GRADLE_FILE="$(find . -type f -name gradlew | head -n 1)"

          if [ -z "$GRADLE_FILE" ]; then
            echo "ERROR: gradlew was not found in the repository."
            echo "Repository contents:"
            find . -maxdepth 3 -type f | sort
            exit 1
          fi

          PROJECT_DIR="$(dirname "$GRADLE_FILE")"

          echo "Android project found at: $PROJECT_DIR"

          cd "$PROJECT_DIR"
          chmod +x gradlew
          ./gradlew assembleDebug --stacktrace

      - name: Collect APK
        shell: bash
        run: |
          mkdir -p apk-output

          find . -type f -name "*.apk" -exec cp {} apk-output/ \;

          echo "APK files:"
          find apk-output -type f -maxdepth 1 -print

          if ! find apk-output -type f -name "*.apk" | grep -q .; then
            echo "ERROR: Build finished without producing an APK."
            exit 1
          fi

      - name: Upload Tourbillion APK
        uses: actions/upload-artifact@v4
        with:
          name: DRC-GOLF-TOURBILLION-APK
          path: apk-output/*.apk
          if-no-files-found: error
