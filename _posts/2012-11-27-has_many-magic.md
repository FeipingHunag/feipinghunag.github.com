---
layout: post
title: "rails with_scope 用法"
category: 
tags: [rails active_record]
---
{% include JB/setup %}

多对多关系设置关系表字段：

	class Membership < ActiveRecord::Base
	  belongs_to :user
	  belongs_to :group
	  #role 
	end
	
	class Group < ActiveRecord::Base
	  has_many :memberships
	  has_many :members, through: :memberships, source: :user
	  has_many :admins, through: :memberships, source: :user,
                    conditions: "memberships.role = 'admin'" do
        def << admin
          Membership.with_scope(create: {:role => "admin"}) { self.concat admin }
        end
      end
	end

	
    class User < ActiveRecord::Base
      has_many :memberships, dependent: :destroy
  	  has_many :groups, through: :memberships, 	source: :group do
    	def create *params, &block
     	  Membership.with_scope(create: {role: 'owner'}) {super}
   	 	end	
 	 end
 
 然后你就可以这样操作：
 
 	user = Use.first
 	group = user.groups.create(name: 'rails topic')
 	#输出
 	BEGIN
 	
 	INSERT INTO "groups" ("created_at", "name", "photo", "updated_at")
 	VALUES ($1, $2, $3, $4, $5) 
 	RETURNING "id"  [
     ["created_at", Tue, 27 Nov 2012 13:54:43 CST +08:00], 
     ["name", "rails topic"], 
     ["photo", nil], 
     ["updated_at", Tue, 27 Nov 2012 13:54:43 CST +08:00]
    ]

	INSERT INTO "memberships" ("created_at", "group_id", "role", "updated_at", "user_id") 
	VALUES ($1, $2, $3, $4, $5) 
	RETURNING "id"  [
	 ["created_at", Tue, 27 Nov 2012 13:54:43 CST +08:00],
	 ["group_id", 333662431702155304], ["role", "owner"], 
	 ["updated_at", Tue, 27 Nov 2012 13:54:43 CST +08:00], 
	 ["user_id", 319781692850044931]
	]
	
	COMMIT
	
	admin = User.last
	group.admins << admin
	#输出
	BEGIN
	
	INSERT INTO "memberships" ("created_at", "group_id", "role", "updated_at", "user_id") 
	VALUES ($1, $2, $3, $4, $5) 
	RETURNING "id"  [
	 ["created_at", Tue, 27 Nov 2012 14:04:54 CST +08:00],
	 ["group_id", 319781694192222215], ["role", "admin"], 
	 ["updated_at", Tue, 27 Nov 2012 14:04:54 CST +08:00], 
	 ["user_id", 328485540292722708]
	]
	
	COMMIT

with_scope： <http://apidock.com/rails/ActiveRecord/Base/with_scope/class>