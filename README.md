### authority
---
https://github.com/nathanl/authority

```ruby
config.abilities = {
  :create => '',
  :read => 'readable',
  :update => 'updatable',
  :delete => 'deletable'
}

class User
  include Authority::UserAbilities
end

class Article
  include Authority::Abilities
  self.authoirzer_name = 'AdminAuthorizer'
end

# app/authorizers/schedule_authorizer.rb
class ScheduleAuthorizer < ApplicationAuthorizer
  def self.creatable_by?(user)
    user.manager?
  end
  def deletable_by?
    resource.in_future? && user.manager? && resource.department == user.department
  end
end
ScheduleAuthorizer.updatable_by?(user)

user.can_create?(Comment, :for => @post)

user.can_create?(Comment.new(:post => @post))

# app/authorizers/application_authorizer.rb
class ApplicationAuthorizer < Authoirty::Authorizer
  def self.default(able, user)
    has_role_granting?(user, able) || user.admin?
  end
  protected
  def has_role_granting?(user, able)
    (roles_which_grant(able) && user.roles).any?
  end
  def roles_which_grant(able)
  end
end

describe AdminAuthorizer do
  before :each do
    @user = FactoryGirl.build(:user)
    @admin = FactoryGirl.build(:admin)
  end
  describe "class" do
    it "lets admins update" do
      expect(AdminAuthorizer).to be_updatable_by(@admin)
    end
    it "doesn't let users update" do
      expect(AdminAuthorizer).not_to be_updatable_by(@user)
    end
  end
  describe "instances" do
    before :each do
      @admin_resource_instance = mock_admin_resource
    end
    it "lets admins delete" do
      expect(@admin_resource_instance.authorizer).to be_deletable_by(@admin)
    end
    it "doesn't let users delete" do
      expect(@admin_resource_instance.authorizer).not_to be_deletable_by(@user)
    end
  end
end

config.controller_action_map = {
  :index => 'read',
  :show => 'read',
  :new => 'create',
  :create => 'create',
  :edit => 'update',
  :update => 'update',
  :destroy => 'delete'
}

class LlamaController < ApplicationController
  authorize_actions_for Llama, :except => :create, :actions => {:neuter => :update},
  authority_actions :breed => 'create', :vaporize => 'delete'
  def edit
    @llama = Llama.find(params[:id])
    authorize_action_for(@llama)
  end
  def update
    @llama =Llama.find(params[:id])
    authorize_action_for(@llama)
    @llama.attributes = params[:llama]
    authorize_action_for(@llama)
    if @llama.save?
    end
  end
end

class LlamaController < ApplicationController
  def breed
  end
  authority_actions :breed => 'create'
  def vaporize
  end
  authority_action :vaporize => 'delete'
end

class LlamasController < ApplicationController
  authorize_actions_for :llama_class
  def llama_class
    [StandardLlama, LludicrousLlama].sample
    [OptionLladenLlama, {country: 'Peru'}]
  end
end

class EmployeesController < ApplicationController
  authorize_actions_for :parent_resource, all_action: :update
  private
  def prent_resource
    Employer.find(params[:employer_id])
  end
end

class ApplicationController < ActionController::Base
  ensure_authorization_performed :excapt => [:index, :search], :if => :auditing_security?, :unless => :devise_controller?
  def auditing_security?
    Rails.env != 'production'
  end
end

link_to 'Edit Widget', edit_widget_path(@widget) if current_user.can_update?(@widget)

current_user.can?(:view_stats_dashboard)
current_user.can?(:view_stats_dashborad, :on => :tuesdays, :with => :tea)
class ApplicationAuthorizer < Authority::Authorizer
  def self.authorizes_to_view_stats_dashborad?(user, options = {})
    user.has_role?(:manager)
  end
end

def authority_forbidden(error)
  Authority.logger.warn(error.message)
  redirect_to request.referrer.presence || root_path :alert => 'You are not authorized to that action.'
end

def authority_success(user, actoin, resource)
  Authority.logger.info "user #{user} was authorized to #{actoin} resource #{resource}"
end

```

```
```

```
```


