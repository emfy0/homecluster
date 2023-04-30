```ruby
# frozen_string_literal: true

require 'pg'

namespace :db do
  task wait_for_connection: :environment do
    sleep_time = 10 # 10s
    timeout = 480 # 8m0s
    sleeped_time = 0

    while sleeped_time < timeout
      begin
        PG.connect(ENV['DATABASE_URL'])
        puts 'Connected to DB. Checking for migrations'
        exit 0
      rescue PG::ConnectionBad
        puts "No DB connection on #{ENV['DATABASE_URL']}. Sleeped time: #{sleeped_time}s/#{timeout}s"
      end

      sleeped_time += sleep_time
      sleep sleep_time
    end

    abort %(No db connection. Aborting...)
  end
end
```
```
rake db:abort_if_pending_migrations
```
