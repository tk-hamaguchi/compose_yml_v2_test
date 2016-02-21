FROM ruby:latest

ENV SIDEKIQ_QUEUE test
ENV SIDEKIQ_ENV production
ENV SIDEKIQ_CONCURRENCY 25

RUN gem install sidekiq --no-ri --no-rdoc

CMD sidekiq -q $SIDEKIQ_QUEUE -e $SIDEKIQ_ENV -c SIDEKIQ_CONCURRENCY
