# git remote update - remote 브랜치 가져오기

{% hint style="info" %}
\*\*\*\*[**GIT remote branch 가져오기**](https://cjh5414.github.io/get-git-remote-branch/)\*\*\*\*
{% endhint %}

## 상황

깃으로 협업할 때 팀원이 만든 브랜치를 확인해봐야 할 때가 있다. 문제는 팀원의 브랜치는 `remote` 에만 존재하고 내 **local** 에는 없다는 것. 보통 이런 경우에는 아래와 같은 `flow` 로 `remote` 브랜치를 가져온다.

```text
# 현재 my_branch
git checkout develop # develop 는 공통 브랜치로 이동
git checkout -b  team_branch # develop 에서 새로운 team_branch 를 만들고 이동
git pull origin team_branch # 리모트에 존재하는 `team_branch`를 땡겨온다.
```

여기서 문제는, _**team\_branch 가 develop 보다 과거일 때!!!**_

`git checkout -b team_branch` 는 현재 `develop` 와 동일한 데이터를 가진 브랜치를 만드는데, remote에 있는 `team_branch` 는 develop 보다 이전의 내용이면 이 `pull` 을 받는 순간 큰일 발생....

## 해결

```text
git remote update
```

이 마법의 명령 하나로 해결. 위의 명령어는 현재 내 local 브랜치와 `remote` 브랜치를 비교해서 `local` 을 `remote` 와 동일하게 만들어준다.

위와 같은 상황이라면 아래와 같은 플로우로 `team_branch` 에 접근하면 된다.

```text
git remote update # 로컬 브랜치 최신화
git checkout team_branch # 끝!
```

