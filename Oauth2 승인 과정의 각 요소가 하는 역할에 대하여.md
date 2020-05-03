> oauth2 사용법과 간단한 이론은 https://opentutorials.org/course/3405/22008 를 참고.

(본 내용은 웹 서비스에서 사용하는 oauth 를 대상으로 함.
chrome extension 에 사용되는 oauth 의 자료가 필요하면 https://developer.chrome.com/extensions/tut_oauth 를 참고하면 됩니다.)

-----------------------------------------------------

oauth2 는 federated identity(https://en.wikipedia.org/wiki/Federated_identity) 
을 하게 해주는 방법 중 하나이며 access token 을 받기까지 과정은 다음과 같이 정리할 수 있습니다.

용어정리:
*resource owner : 브라우저를 사용하는 일반이용자
*client : 웹 서비스를 제공하고 있는 서버
*resource owner : client가 resource owner 의 허락을 받아 사용하려고 하는 자원을 가지고 있는 대상 서버 (google, facebook, twitter 등)


1. resource owner 가 client 에서 제공하는 웹사이트에서 oauth 승인 url 을 클릭
   -> resource server 로 client id, redirect url, scope 등이 전달됩니다.
   
2. resource server 에서 client id 가 등록되어있는지 확인하고 받은 redirect url 이 등록된 url 과 동일한지 확인합니다. 
2.1 resource owner 에게 동의를 받고나면 resource server 에 user id 와 scope 가 저장됩니다.
   -> owner 에게 응답으로 redirect 주소와 함께 "outhorization code" 를 붙여서 해당 url 로 이동하게 합니다.
   
3. outhorization code 를 받은 client 서버는 (redirect url 도메인은 client 서버일것) 직접 resource server 에 직접 요청을 보냅니다. 
   -> 여기서 client id, authorization code, client secret, redirect url 가 전달됩니다.
   -> 보낸 정보가 resource server 에 저장된 정보와 모두 일치하면 response 로 access token 을 받을 수 있습니다.
   

#####각 과정의 요소들이 없다면?:
(**지극히 개인적인 관점에서 설명**)

1. client id 개념 없이 유저 승인만으로 access token 을 발급해주면 어떻게 될까 ?
> access token 이 남용되고 여기저기 뿌려져도 책임질 client 를 모른다.

2. redirect url 은 요청 url 에 적혀있는 client서버가 승인 결과를 바로 받고 처리할 수 있도록 한다.

3. client secret 은 outhorization code 를 받은 client만이 access token 을 받을 수 있도록 하는 역할을 한다.
> client server 소유자가 아니어도 웹페이지에 올라와있는 resource owner 승인 url 에서 client id, redirect url 는 바로 알 수 있고,
응답으로 받은 redirect url 에서 outhorization code 도 볼 수 있으므로, 만약 client secret 이 타인에게 노출된다면 access token 발급 요청
url 을 만들 수 있어 client 가 아닌 사람이 owner 가 승인한 resource server 의 기능을 사용할 수 있게 된다.

4. owner 에게 승인을 받는 과정과 client 가 resource server 의 승인을 받는 과정이 분리되어있어야하는 이유.
> 만약 owner 승인 이후에 redirect url 에게 post data 로 바로 access 토근을 주면 resource owner 가 access token 을 직접 볼 수 있는데,
악용될 가능성과 , 심지어 실제 resource owner 가 아닌 사람이 볼 가능성도 있을 것. 
