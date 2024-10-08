# devise-jwt-sample-on-rails-5 README

Minimum use of [devie-jwt](https://github.com/waiting-for-dev/devise-jwt) gem in rails 5 to confirm how it works.  
See commit messages to know how to make the same app by rails commands.

# Receive and use JWT
Clone the repository, run migrations and start rails
```
rails db:create
rails db:migrate
bundle config set --local path 'vendor/bundle'
bundle install
rails s
```

Create a user and recieve JWT

```
curl -X POST http://localhost:3000/users -H "Content-Type: application/json" -d "{ \"user\": { \"email\": \"test@test.com\", \"password\": \"password\" }}" -v
```

```
...
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI0NGU4.....3cktz33QYfgKtjw
...
```

Sign-in by the user and recieve JWT
```
curl -X POST http://localhost:3000/users/sign_in -H "Content-Type: application/json" -d "{ \"user\": { \"email\": \"test06@test.com\", \"password\": \"password\" }}" -v
```

```
...
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiIzMzQ2.....ly4veYpWdezY-sSkI-ROTw_E
...
```

Sign out using JWT
```
curl -X DELETE http://localhost:3000/users/sign_out -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiIzMzQ2.....ly4veYpWdezY-sSkI-ROTw_E" -v
```

`users.jti` is updated to revoke the JWT
```
User Update (3.7ms)  UPDATE `users` SET `jti` = 'd53871ff-01ac-464f-9cda-6527b1f9378a' WHERE `users`.`id` = 5
```

# Decode JWT
```
rails c
JWT.decode("eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiJkNTM4NzFmZi0......vyN-3y7tzymqDRQU3aG0",Rails.application.credentials.fetch(:secret_key_base))
```

Decode Succeeded
```
=> [{"jti"=>"d53871ff-01ac-464f-9cda-6527b1f9378a", "sub"=>"5", "scp"=>"user", "aud"=>nil, "iat"=>1724143626, "exp"=>1724145426}, {"alg"=>"HS256"}]
```

Failed
```
JWT::VerificationError (Signature verification failed)
```
```
JWT::ExpiredSignature (Signature has expired)
```

# Interface
```
$ rails routes
                   Prefix Verb   URI Pattern                                                                              Controller#Action
         new_user_session GET    /users/sign_in(.:format)                                                                 devise/sessions#new
             user_session POST   /users/sign_in(.:format)                                                                 devise/sessions#create
     destroy_user_session DELETE /users/sign_out(.:format)                                                                devise/sessions#destroy
        new_user_password GET    /users/password/new(.:format)                                                            devise/passwords#new
       edit_user_password GET    /users/password/edit(.:format)                                                           devise/passwords#edit
            user_password PATCH  /users/password(.:format)                                                                devise/passwords#update
                          PUT    /users/password(.:format)                                                                devise/passwords#update
                          POST   /users/password(.:format)                                                                devise/passwords#create
 cancel_user_registration GET    /users/cancel(.:format)                                                                  devise/registrations#cancel
    new_user_registration GET    /users/sign_up(.:format)                                                                 devise/registrations#new
   edit_user_registration GET    /users/edit(.:format)                                                                    devise/registrations#edit
        user_registration PATCH  /users(.:format)                                                                         devise/registrations#update
                          PUT    /users(.:format)                                                                         devise/registrations#update
                          DELETE /users(.:format)                                                                         devise/registrations#destroy
                          POST   /users(.:format)                                                                         devise/registrations#create
       rails_service_blob GET    /rails/active_storage/blobs/:signed_id/*filename(.:format)                               active_storage/blobs#show
rails_blob_representation GET    /rails/active_storage/representations/:signed_blob_id/:variation_key/*filename(.:format) active_storage/representations#show
       rails_disk_service GET    /rails/active_storage/disk/:encoded_key/*filename(.:format)                              active_storage/disk#show
update_rails_disk_service PUT    /rails/active_storage/disk/:encoded_token(.:format)                                      active_storage/disk#update
     rails_direct_uploads POST   /rails/active_storage/direct_uploads(.:format)                                           active_storage/direct_uploads#create
```

