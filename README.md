이 리포지토리에는 다음을 사용하여 Electron 앱을 자동 업데이트하는 **최소 코드**가 포함되어 있습니다.

[`electron-updater`](https://github.com/electron-userland/electron-builder/tree/master/packages/electron-updater) with releases stored on GitHub.

GitHub를 사용할 수 없는 경우 다른 공급자를 사용할 수 있습니다.

- [Complete electron-updater HTTP example](https://gist.github.com/iffy/0ff845e8e3f59dbe7eaf2bf24443f104)
- [Complete electron-updater from gitlab.com private repo example](https://gist.github.com/Slauta/5b2bcf9fa1f6f6a9443aa6b447bcae05)

**참고:** 이 전체 프로세스를 실행하려면 이 저장소를 분기하거나 [템플릿에서 직접 시작](https://github.com/iffy/electron-updater-example/generate)하십시오. 그런 다음 다음 단계를 수행하기 전에 'iffy'의 모든 인스턴스를 당신의 GitHub 사용자 이름으로 바꿉니다.

---
### 1. macOS의 경우 코드 서명 인증서가 필요합니다.

(App Store에서) Xcode를 설치한 다음 [이 지침](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW6)을 따르세요.
    "Developer ID Application" 인증서가 있는지 확인하십시오.
    인증서를 내보내려는 경우(예: 자동화 빌드용) [이 지침](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW7)을 따르십시오.
    그 후에 [이 지침](https://www.electron.build/code-signing)을 따르십시오.
   
이 예제 응용 프로그램은 'Developer ID Application' 인증서가 기본 키체인에 설치된 경우 Mac OS에서 코드 서명 및 공증을 수행하도록 설정됩니다. 다음 환경 변수는 서명 프로세스에 중요합니다.

- `CSC_IDENTITY_AUTO_DISCOVERY` - `electron-builder`가 애플리케이션 서명을 시도하는지 여부를 제어합니다. 기본값은 'true'이고 서명을 건너뛰려면 'false'로 설정합니다.
- `APPLE_ID` - 공증에 사용할 Apple ID(서명에 필요).
- `APPLE_ID_PASSWORD` - 공증을 위해 지정된 Apple ID와 함께 사용할 암호(서명에 필요), Apple ID 암호를 보호하기 위해 앱 암호를 설정할 것을 권장합니다. 자세한 내용은 ([Apple Support](https://support.apple.com/en-us/HT204397)) 참고.

code-signing과 notarization을 활성화 하려면:
    
    export CSC_IDENTITY_AUTO_DISCOVERY="true"
    export APPLE_ID="<your Apple ID>"
    export APPLE_ID_PASSWORD="<your Apple Password>"

### 2. 필요한 경우 `package.json` 수정.

기본적으로 `electron-updater`는 `.git/config`를 읽거나 `package.json` 내의 다른 속성을 읽는 것으로부터 GitHub 설정(예: repo 이름 및 소유자)을 감지하려고 시도합니다. 자동 감지된 설정이 원하는 것과 다른 경우 아래 내용과 같이 [`publish`](https://github.com/electron-userland/electron-builder/wiki/Publishing-Artifacts#PublishConfiguration)를 구성하십시오.

        {
            ...
            "build": {
                "publish": [{
                    "provider": "github",
                    "owner": "iffy",
                    "repo": "electron-updater-example"
                }],
                ...
            }
        }

### 3. 필수 dependencies 설치:

        yarn

   or

        npm install

### 4. <https://github.com/settings/tokens/new>로 이동하여 GitHub 액세스 토큰을 생성하고, 환경 변수에 할당하십시오.
액세스 토큰에는 'repo' 범위/권한이 있어야 합니다. 토큰이 있으면 환경 변수에 할당하십시오.

    On macOS/linux:

        export GH_TOKEN="<YOUR_TOKEN_HERE>"

    On Windows, run in powershell:

        [Environment]::SetEnvironmentVariable("GH_TOKEN","<YOUR_TOKEN_HERE>","User")

    최신 환경 변수를 상속하려면 IDE/터미널을 다시 시작해야 합니다.

### 5. 플랫폼 별 Publish:

        electron-builder -p always

   or

        npm run publish

더 많은 플랫폼에 게시하려면 `package.json`에서 `publish` 스크립트를 편집하세요.
예를 들어 Windows 및 macOS용으로 빌드하려면 다음을 수행합니다.

        ...
        "scripts": {
            "publish": "electron-builder --mac --win -p always"
        },
        ...

참고: Mac OS 서명/공증 프로세스는 Mac OS에서 실행해야 합니다.

### 6. GitHub에서 Release 하십시오.

### 7. Release된 앱을 다운로드하여 설치합니다.

### 8. `package.json`의 버전을 업데이트하고 GitHub에 커밋하고 푸시합니다.

### 9. 5단계(Publish)와 6단계(Release)를 다시 수행합니다.

### 10. 설치된 앱 버전을 열고 자체적으로 업데이트되는지 확인합니다.
