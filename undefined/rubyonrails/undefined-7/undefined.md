# 연관 모델을 다른 이름으로 설정하고 가져오기

## 레일즈 연관모델이 가져와지지 않는다?

## 상황

```ruby
# guild_invitation.rb
class GuildInvitation < ApplicationRecord
  scope :for_user_index, -> (user_id) do
    where(invited_user_id: user_id, result: "pending").order(created_at: :desc).map do |invitation|
      {
        id: invitation.id,
        sender: invitation.user.name,
        receiver: invitation.invited_user.name,
        guild: invitation.guild.to_simple,
      }
    end
  end
  belongs_to :guild
  belongs_to :user
  belongs_to :invited_user, class_name: "User"
end
```

```ruby
# user.rb
class User < ApplicationRecord
...
  has_many :guild_invitations, foreign_key: "invited_user_id"
...
end
```

모델 정의를 보면 **guild\_invitation** 이 **user** 에 메달려있는걸 알 수 있다.

따라서 **user.guild\_invitations** 문법을 사용하면 **user** 에 매달려 있는 모든 **guild\_invitation** 을 가져올 수 있을거다.

**하지만, 지금 user.guild\_invitations 이 동작하지 않는다!!, 아니 잘동작할때도 있고 아닐때도 있다.**

```ruby
user = User.find(1)
invited_user = User.find(2)
invitation = GuildInvitation(user_id: user, invited_user_id: invited_user, guild_id: 1)

user.guild_invitations # nil

invited_user.guild_invitations # <guild_invitations ... >
```

## 해결

원인을 알기 위해서는 **guild\_invitation** 의 마이그레이션 파일과 위에있는 **guild\_invitation.rb** 를 봐야한다.

```ruby
# migration
class CreateGuildInvitations < ActiveRecord::Migration[6.1]
  def change
    create_table :guild_invitations do |t|
      t.references :user, null: false, foreign_key: true
      t.references :guild, null: false, foreign_key: true
      t.references :invited_user, null: false, references: :users, foreign_key: { to_table: :users }
      t.string :result, null: false, default: "pending"
      t.timestamps
    end
  end
end
```

**guild\_invitation** 은 자신의 속성으로 초대를 한 유저, 초대를 받은 유저 처럼 필연적으로 같은 테이블이 다른 이름으로 들어가야함을 알 수 있다. 우리는 초대를 한 유저는 **user**고 초대를 받은 유저는 **invited\_user** 가 된다.

마이그레이션 파일을 보면 **invited\_user** 를 **레퍼런스** 로 받는데 **invited\_user** 에 해당하는 모델이 없으므로 **references** 라는 옵션으로 **users** 에 연결한 걸 확인할 수 있다.

**guild\_invitation.rb** 파일을 보면 `belongs_to :invited_user, class_name: "User"` 라는 문구가 있다. 그리고 **user.rb** 에서 `has_many :guild_invitations, foreign_key: "invited_user_id"` 가 있다.

즉 **user** 레코드를 이용해 **guild\_invitation** 에 접근하기 위해서 사용하는 레퍼런스는 **user** 가 아니라 **invited\_user** 였던것.

```ruby
GuildInvitation.create([
  {user_id: 1, invited_user_id: 55, guild_id: 1},
  ])
```

따라서 위와같은 시드가 있을 때 **User.find\(1\).guild\_invitations** 는 비어있고, **User.find\(55\).guild\_invitation** 을 하면 초대장이 잘 뜬다.

만약 초대를 한 사람도 자신이 보낸 **guild\_invitations** 를 보고 싶다면 **user.rb 파일에** `has_many :guild_invitations,` 만 추가해주면 된다.

