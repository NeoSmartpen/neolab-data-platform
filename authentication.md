### 2. 인증

------

###### 개요

NDP의 서비스와 리소스에 접근하고 사용하려면 인증 절차를 거쳐야 합니다. NDP의 인증은 토큰 기반의 인증 방식(JWT)을 기반으로 합니다.  
인증을 위한 기본 절차는 다음과 같습니다. 

```
1. NDP Developer Center에서 인증서를 발급 받습니다.
2. 애플리케이션은 발급 받은 인증서와 필요시 사용자(회원) 로그인으로 NDP 인증 서버에서 인증 코드(Authorization code)를 발급 받습니다.
3. 애플리케이션은 인증 코드로 NDP 인증 서버에서 접근 토큰(Access Token)을 발급 받습니다.
4. 애플리케이션은 발급 받은 접근 토큰으로 NDP의 리소스와 서비스를 이용합니다.
```

사용자(회원) 단위 서비스를 사용하기 위한 인증으로 NDP는 1) NDP 자체 로그인을 통한 인증과 2) 외부 서비스(구글, 애플 등)의 로그인 API를 통한 인증 두 가지를 모두 지원합니다.  
애플리케이션에서는 NDP에서 제공하는 서비스로 보다 편리하게 로그인 기능을 구현하고, 사용자를 인증하여, 회원 정보를 조회할 수 있습니다.

###### 전체 구조도

NDP는 서비스 분류에 따라 인증 절차가 다릅니다. 다음은 인증 구조를 간략하게 표현한 그림입니다.

*사용자(회원) 단위 서비스*
1. 자체 로그인 인증
2. 소셜 로그인 인증

*리소스오너 단위 서비스*



###### 용어 · 요소 설명

* **인증서 (Credentials)**  
인증서는 NDP의 서비스 및 리소스에 접근하기 위한 자격 증명서입니다. NDP는 인증서를 통해 인증된 사용자임을 확인합니다. 리소스오너는 NDP Developer Center에서 인증서를 발급 받을 수 있습니다. 리소스오너는 한 애플리케이션 내에 여러 개의 인증서를 생성할 수 있습니다. 그리고 각 인증서마다 API 사용 범위(Scope)와 리소스 접근 권한(Authority)을 다르게 부여할 수 있습니다.  

   인증서는 서비스 인증 단위에 따라 크게 두 가지로 나뉩니다. 
  * **사용자 인증서**  
사용자(회원) 단위 서비스 인증에서 사용합니다. 해당 서비스는 애플리케이션에 속한 서비스로, 애플리케이션 사용자의 로그인이 필요합니다. 따라서, 애플리케이션 사용자를 단위로 하는 사용자 인증서를 발급 받아야 합니다.  

    사용자 인증서는 두 가지로 나뉩니다.
    - 자체 인증서 (OAuth2.0 Credentials) : NDP 자체 로그인으로 접근 토큰을 받을 수 있는 인증서입니다. NDP의 인증 서비스는 OAuth 2.0을 기반으로 합니다. 
    - 외부 인증서 (External Credentials) : 외부(구글, 애플 등)의 로그인 서비스를 사용하기 위해 얻은 인증서(Credentials)로 NDP의 접근 토큰을 받을 수 있는 인증서입니다. NDP Developer Center에서 인증서를 생성할 때, 외부 서비스에서 발급 받은 인증서의 정보를 함께 입력해야 합니다.  
  
  * **서비스 인증서**  
      서비스 단위의 인증에서 사용합니다. 해당 서비스는 리소스오너가 직접 인증하고 사용하는 서비스로, 사용자(회원)의 로그인 없이 서비스에 접근할 수 있습니다. 따라서 리소스오너를 단위로 하는 서비스 인증서(Service Credentials)를 발급 받습니다. 이러한 인증서는 로그인 인증이 없이 서비스에 접근할 수 있기 때문에, 유출되지 않도록 더욱 신경써야 합니다. 

- **API 범위? 범위? (Scope)와 권한 (Authority)**  
  범위는 인증서가 호출할 수 있는 API의 범위입니다. 권한은 인증서가 접근할 수 있는 리소스를 나타냅니다. 예를 들면, 사용자 데이터를 조회하는 userdata.read, 서식을 관리하는 paper.owner가 있습니다. 
  리소스오너는 인증서를 생성할 때 범위와 권한을 선택하여 생성해야 합니다. 범위와 권한 중 일부는 네오랩의 승인이 필요할 수 있습니다. 리소스오너가 선택하지 않았거나 네오랩에서 승인하지 않은 범위의 API는 호출할 수 없습니다.

- **OAuth 2.0**  
  [OAuth 2.0](https://tools.ietf.org/html/rfc6749)은 인증과 권한 부여를 위해 사용하는 표준 프로토콜입니다. OAuth 2.0은 제 3의 앱이 자원의 소유자인 서비스 사용자(회원)를 대신하여 서비스를 요청할 수 있도록 자원 접근 권한을 위임하는 방법입니다. (출처 : 금융보안원 "OAuth 2.0 개요 및 보안 고려사항" 보안연구부-2015-030)

- **인증 코드(Authorization Code)**  
  인증 코드는 애플리케이션에서 NDP의 접근 토큰을 얻는 데 사용하는 코드입니다. 애플리케이션에서 NDP에게 인증을 요청하면, NDP는 애플리케이션에서 사용자 로그인을 요청합니다. 사용자의 아이디, 패스워드 검증과 사용자의 권한 동의가 성공적으로 끝나면, 인증서를 발급 받을 때 등록한 redirect_uri로 인증 코드를 전송합니다. 

- **접근 토큰(Access Token)과 리프레시 토큰(Refresh Token)**  
  접근 토큰은 인증에 성공하면 NDP에서 애플리케이션에게 보내는 키입니다. 애플리케이션에서 NDP의 API를 호출할 때, 요청 헤더에 접근 토큰을 함께 보내야 합니다. NDP는 접근 토큰을 검증하여 NDP 서비스를 사용할 권한을 부여합니다. 
  이러한 접근 토큰은 수명이 있습니다. 리프레시 토큰은 애플리케이션에서 일정 기간 동안 인증을 거치지 않아도 접근 토큰을 갱신할 수 있는 역할을 합니다. 리프레시 토큰을 통해 사용자는 매번 로그인하지 않아도 접근 토큰을 발급 받을 수 있습니다. 단, 인증 절차에 사용자의 로그인이 필요하지 않은 리소스오너 단위 서비스에서는 리프레시 토큰이 발급되지 않습니다. 