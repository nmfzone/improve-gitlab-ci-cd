# Improve Gitlab CI/CD

To improve the Gitlab CI/CD speed, there is a lot of technique. I'll show you some example.
In this example, I use Gitlab CI/CD for my NuxtJs application.

## Using Cache

Gitlab gives us ability to cache the files on each stage. So, in this example, I need to cache the:

1. node_modules
2. .nuxt
3. build (because I used Backpack, with my own server implementation)
4. Others

To achive this, you just need to add the cache config in your `.gitlab-ci.yml`.
```
cache:
  key: "$CI_COMMIT_REF_SLUG"
  paths:
    - .nuxt/
    - build/
    - .env
    - .test.env
    - node_modules/
```

By using this method, I just need to `build` the project one time (on the `ci_prepare` stage), then for subsequent stage I just need to re-use it.

In my example, I've 2 tests stage that need Nuxt to be build first, to perform the test. So, instead I build the Nuxt in every stage (that each build needs ~6 minutes), I just need to create one stage named `ci_perepare`, then build once in that stage.

## Using slim docker image

To improve the speed of Gitlab CI/CD, you should use slim docker image as much as possible.
For example, to run deploy stage, that only require `ssh`, I use `nmfzone/ssh-client-light` image, that use alpine linux, so it's just need to pull 5 MB image.
