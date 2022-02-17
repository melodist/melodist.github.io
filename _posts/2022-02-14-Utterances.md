---

layout: post
title: Github Blog에 Utterances 추가
tags: [Github, Jekyll, Utterances, UF]
permalink: /docs/DB/MySQLwithDocker
date: 2022-02-14 12:05:00

---

# Github Blog에 Utterances 추가

### 배포 시 오류 발생

```bash
Bundler could not find compatible versions for gem "bundler":
  In Gemfile:
    bundler (~> 2.2.8)

  Current Bundler version:
    bundler (2.3.7)

Your bundle requires a different version of Bundler than the one you're running.
Install the necessary version with `gem install bundler:2.2.33` and rerun
bundler using `bundle _2.2.33_ install`
```

### .github/workflows/ci-master.yml 수정

```bash
 jekyll/builder:latest /bin/bash -c "gem install bundler && chmod -R 777 /srv/jekyll && bundle install && bundle exec jekyll build && bundle exec rake search:init"
```

```bash
jekyll/builder:latest /bin/bash -c "gem install bundler:2.2.33 && chmod -R 777 /srv/jekyll && bundle install && bundle exec jekyll build && bundle exec rake search:init"
```


## 참고

[Utterances](https://github.com/apps/utterances)