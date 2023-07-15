---
title: Creating a User in Mastodon from the Interactive Ruby Shell
date: 2023-07-14 19:56:28
tags:
- mastodon
- ruby
---

## Why
I found myself wanting to create a bot account for my [Mastodon installation](https://scz.social).

But, I didn't want to have to set up and configure an email and go through the whole account confirmation email.

These commands did it and let me log in and create an app and start posting.

At the time of writing this applies to at least Mastodon 4.0.6.

## How

Enter the Ruby interactive shell as the `mastodon` user.

```bash
~/live$ whoami
mastodon

~/live$ RAILS_ENV=production bundle exec rails c
Loading production environment (Rails 6.1.7.4)
irb(main):001:0> 
```

Create the `Account` record, followed by the `User` record.

```rb
account = Account.create!(username:'username_of_new_account')
user = User.create!(email: 'username_of_new_account@youmasto.social', password: 'better_change_me', account: account, agreement: true)
```

Take some actions to confirm the account, without needing to actually have an email and persist the records.

```rb
user.confirm
account.save!
user.save!
```

Go login to the account and do what you need to do.