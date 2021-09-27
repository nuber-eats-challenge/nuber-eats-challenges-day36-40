# nuber-eats-challenges-day36-40

<details>
  <summary>
  Day 31-33 정답해설
  </summary>

### ```src/pages/__test__/create-account.spec.tsx```
1. 기본 구조 및 테스트 방법
- 기본 구조
- ![](https://i.ibb.co/mGHgmHC/basic-structure.png)
- frontend 유닛테스트의 기본구조입니다. apollo client를 이용하여 데이터를 받아오는 부분 때문에 apollo를 mocking 할 필요가 있습니다. 위와 같은 방법 이외 MockedProvider를 이용하는 방법도 있으니 참고 바랍니다.
- 테스트 로직: 백엔드에서 했던 것과 유사합니다. 지금 설명드릴 예정인 create-account를 예를 들어 설명드리면, 기본적으로 create-account에서는 유저에게 계정에 대한 입력을 받습니다. 그러면 앱에서는 들어온 입력이 올바른 이메일 계정, 패스워드 길이 등을 판단하여 그렇지 않다면 오류메세지를 내뿜고, 올바르다면 submit을 할 수 있게 되어 계정 생성을 프론트 엔드에 요청을 하게 됩니다. 그렇다면, 테스트 해야 할 것들은 첫째 입력이 잘못되었을 때, 에러 핸들링을 잘하고 있는가, 둘째 입력이 잘 되었을 때 생성 요청에 대한 핸들링이 잘 되는가 입니다. 로그인을 테스트한다면.. 마찬가지 로직이겠죠? 크게 차이 나진 않겠죠?
2. create-account의 로직
- 앞서 말씀드린대로 테스트는 유저에게 입력을 받고 받은 입력이 올바른지 파악해야 합니다. 그렇다면 그 다음과정은, 아마도 아래와 같을 것입니다.
- (1) 올바르지 않다면 에러메세지 출력
- (2) 올바르다면 백엔드에 createAccount Mutation 보내고, 루트 페이지로 이동하기 입니다.
- ![](https://i.ibb.co/gg03Tdv/test-structure.png)
- 그래서 위 코드처럼 테스트를 진행할 수 있습니다.
3. 유저입력이 올바르지 않을 때 에러 핸들링 테스트
- 위 코드의 renders validation errors에 해당하는 부분입니다.
- ![](https://i.ibb.co/vq88SmY/mocked-input.png)
- 위 코드의 첫번째 줄 renderResult는 테스트 셋팅 부분인 beforeEach부분에서 설정된 부분입니다. 렌더링 결과에 대해 html을 선택할 수 있게끔 만들어진 함수 집합들입니다. email 엘리먼트는 placeholder가 정규표현식으로 email을 포함하는데, case insensitive하게 검색을 하여 선택을 하였습니다. button은 role 속성이 button인 것을 검색하여 선택하도록 하였습니다. 그리고 email 엘리먼트에는 wont @work 라는 잘못된 이메일을 userEvent를 통해 입력하도록 하여 그 후의 코드에서 테스트 할 수 있게 하였습니다.
4. 올바른 입력을 받아서 계정 만들기
- 이번에는 userEvent를 통해 이메일과 패스워드, role에 올바른 입력값들을 넣어주어서 가짜 서버에 createAccount를 요청합니다.
- ![](https://i.ibb.co/y6k4Lfz/onvalid-submit.png)
- 위 코드에서 mockedClient.setRequestHandler가 보이시나요? apollo client에 CREATE_ACCOUNT_MUTATION이라는 요청이 들어오면, mockedCreatedAccountMutationResponse로 응답을 리턴하라는 의미입니다. 유닛테스트이기 때문에 클라이언트에서 요청하는 서버 응답값은 흉내내는 것입니다. 그리고 요청한 값들에 대해 제대로 처리 되었는지 확인하면 create account테스트는 완료됩니다.
5. 패키지 일부함수만 mocking하기
- 풀커버리지를 해야 하는 것을 고려하면 최대한 많은 부분에서 테스트 해야 합니다. 그래서 useHistory 역시 테스트 대상입니다. history.push('/'); history는 react-router-dom 패키지의 useHistory에 있습니다. 이것을 mocking 하려면 아래 그림의 코드와 같이 해주셔야 합니다.
- ![](https://i.ibb.co/qd5bYFb/mock-history.png)
- 위의 코드처럼 작성을 하시고 expect를 이용하여 history.push를 호출 했는지 여부를 테스트 할 수 있게 되며, 조금더 풀커버리지에 가까워졌습니다.

### ```src/pages/__test__/getAllPodcasts.spec.tsx```
1. 간단하게 getAllPodcasts의 테스트 로직만 살펴보고 넘어가겠습니다. 왜냐하면 테스트 하실 것들은 많은데 분량 관계로 제가 여기서 설명을 다 드릴 순 없을 것 같습니다. 솔루션의 getAllPodcasts.tsx를 살펴 보시면 로직은 간단하죠? 첫째, 팟캐스트 데이터들을 받아서, 둘째, 렌더링한다. 끝입니다.
2. data fetching
- data는 어디서 오죠? 서버에서 gql을 이용하여 끌어 오는거죠? 유닛테스트에서는 거기까지 테스트할 순 없죠? 그럼 mocking하면 됩니다. mocking을 한 후에, mocking한 함수가 호출되었는가? 호출되었는데 내가 원하는 데이터를 입력하고, 원하는 데이터를 받아오나? 체크하시면 됩니다.
- 솔루션의 getAllPodcasts.spec.tsx 파일을 보시면 꽤나 길어 보이시지만, 데이터가 대부분입니다. 데이터 중에 특히나 주의여겨 보셔야 할 것은 __typename 입니다. graphql이 데이터 처리를 위해 타입을 확인하는 것이라서 __typename을 지정해주셔야 합니다. 그렇지 않으시면 테스트 결과가 원하는대로 처리되지 않을 가능성이 높습니다.
- 참고. ApolloProvider에 mocked client를 입력한 것이 아니라 MockedProvider를 이용하셨다면 addTypename={false} 옵션을 이용하실 수도 있습니다.
3. 렌더링 테스트
- render함수를 이용하여 renderResult를 받고 나서 원하시는 테스트를 하시면 됩니다. 위에 mocking한 client에서 받은 텍스트를 가진 엘리먼트가 있는지 getByText를 이용하셔도 좋고, 클래스를 찾으셔도 좋고 테스트 방법은 원하시는대로 해보시면 될 것입니다. 이렇게 하면 getAllPodcasts에 대한 테스트도 끝났습니다.

### 참고
form의 handleSubmit의 콜백함수 같은 경우에는 테스트가 어렵습니다. 안에 들어가있는 콜백함수를 mocking하지 않으니 콜이 되었는지 여부를 모를 수 있습니다. enzyme과 같은 패키지를 사용하거나 블루프린트에 세팅되어 있는 test-utils의 fireEvent 등을 이용해서 가능하다고 하는 글도 있지만, handleSubmit안에 있기 때문에 mocking하기가 쉽지 않습니다. 참고바랍니다.
### 결론
유닛 테스트를 하실 때에는 먼저 어떤 로직으로 했으며, 이 로직을 어떤 경우의 수로 나눠서 테스트할까 고려해보시면 처음에는 어려우실 수 있는데, 후반부터는 쭉쭉 나아갈 수 있습니다. 이 챌린지 이후에는 백엔드도 테스트하실 수 있고, 프론트엔드도 유닛 테스트 하실 수가 있게 되었습니다. 풀커버리지 만큼이나 풀스택에 조금더 가까워질 수 있는 챌린지 과제였습니다.

</details>

### Let's finish this!
- 오늘의 강의: 우버 이츠 클론코딩 강의 #24.0 - #24.3 (21.09.27 - 10.01)
- 오늘의 과제: 위의 강의를 시청하신 후, 아래 코드 챌린지를 제출하세요.

### Code Challenge

이제 마무리 시간입니다. 이번주 토요일 오전 6시까지 챌린지의 podcast discovery app을 완성하시고, 프론트엔드를 Netlify에 deploy하셔서 마무리 하세요. 아래는 구현할 것들입니다.

##### Listener & Host:

- Create Account
- Log in
- Edit Profile

##### Host:

- Create a Podcast
- Edit a Podcast
- Delete a Podcast
- See Podcast Dashboard (See Listeners and Reviews)
- Upload an Episode
- Edit an Episode
- Delete an Episode

##### Listener:

- See Podcast Categories
- Search Podcasts
- See Podcast + Episodes
- Review Podcast
- Subscribe to a Podcast
- See Subscriptions
- See Feed (Episodes of Podcasts the user is subscribed to combined)

###### React를 이용하여 Audio player를 직접 구현하시면 가산점이 있습니다



<details>
  <summary>
  Hint
  </summary>

- 마지막 과제입니다. 위의 구현할 것들을 보시면 여태까지 구현 안했던 것들도 있죠? 그럼 프론트엔드와 백엔드를 모두 수정하셔야 합니다.
- 당연한 이야기지만 백엔드 구현에도 신경을 많이 써주셔야 합니다. Upload Episode를 구현하시려면 파일 업로드도 구현 하셔야겠죠?? 강의에서 다뤘던 /upload 등의 경로를 이용하셔서 s3 등에 업로드 하는 방식으로 구현하시는 것을 추천드립니다
- 구현하기 어렵다고 포기하지 마시고 하나씩, 하나씩, 천천히 구현해 나가시길 바랍니다. 어려운 부분은 주저 마시고 슬랙이 질문 하셔서 꼭 챌린지 과제 완성하여 제출하시길 바랍니다~!
- 프론트엔드는 거의 창작의 영역이라 힌트 드릴 것이 없네요. 익숙치 않고 어려우시다면,, 일단 구현 먼저 하시고 다음에 꾸미시길 추천드립니다.
</details>