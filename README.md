# kakao_flutter_sdk

이 라이브러리는 https://github.com/kakao/kakao_flutter_sdk 를 기반으로 uni_links 를 이용해 딥링크를 구현한 환경에서도 잘 동작하게 하기 위한 라이브러리입니다. 라이브러리 버전은 1.4.2를 기준으로 합니다.

## 이용법
```yaml
dependencies:
  flutter:
    sdk: flutter
  ...
  kakao_flutter_sdk: ^1.4.2
  kakao_flutter_sdk_share: ^1.4.2 # 카카오톡 공유
dependency_overrides:
  kakao_flutter_sdk_common:
    git: https://github.com/KimWash/kakao_flutter_sdk_common
```
pubspec.yaml에 다음과 같이 dependency_overrides 란에 추가해주시면 됩니다.

## 어떻게 해결했어요?
이 이슈와 관련있습니다. https://github.com/avioli/uni_links/issues/63 

카카오톡 스킴이 아닌 경우에도 application 메서드가 true를 리턴해 uni_links에서 이벤트를 못받는 것으로 추측됩니다. 자세한 것은 https://github.com/kakao/kakao_flutter_sdk/issues/86 를 참고해주세요.
```swift
   public func application(_ application: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        let urlString = url.absoluteString
        if(redirectUri != nil && urlString.starts(with: "kakao") && urlString.contains(Constants.oauthPath)) {
            if(urlString.hasPrefix(redirectUri!)) {
                self.authorizeTalkCompletionHandler?(url, nil)
                return true
            } else {
                self.authorizeTalkCompletionHandler?(nil, FlutterError(code: "REDIRET_URL_MISMATCH", message: "Expected: \(redirectUri!), Actual: \(url.absoluteString)", details: nil))
                // If the redirect uri set in sdk is different from the uri received from the server, sdk cannot handle it, so return false
                return false
            }
        } else if(urlString.starts(with: "kakao") && (urlString.contains(Constants.talkSharingPath) || urlString.contains(Constants.storyPath))) {
            eventSink?(urlString)
            return true
        }
        return false
    }
```
