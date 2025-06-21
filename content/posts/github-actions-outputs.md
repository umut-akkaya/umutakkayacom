---
title: 'Github Actions Outputs'
date: 2025-06-21T19:27:51+02:00
draft: false
type: 'page'
author: Umut AKKAYA
---
# Cannot pass information between jobs in Github Actions if it contains secret values

Passing information between Github actions steps is one of the most necessary features. Github Actions supports multiple way of doing this. Normall a job is a seperated environment. It means you cannot reach the resources between jobs. However sometimes you need to transfer some information and files between jobs and steps in order to shape the workflows behivaior.

Github Actions allows us multiple ways to do it. I will mention most of the used ones before i get to the point. These are;

- Artifacts
- Environment Variables
- Job Outputs

These 3 methods are widely used and covers most of the need. Let's quicly have a recap on these.

## Artifacts

Most of the workflows produces some kind of files such as docker images, configuration files, caches etc. After the producement of this files we might use these files in the following jobs or steps. If we are going to use them in the same job, there is no problem. Because the job uses the same storage and files are located in our runner (Machine that executes our worklow). However they are needed in the other jobs we cannot get these files unless they are running in the same machine.

To overcome this problem we can use the Artifacts Actions. With Github developed `actions/upload-artifact` and `actions/download-artifact` we can achive this goal. As an example following workflow configuration allows us to transfer files under `tey` folder and use them in the next job

```yaml
on: workflow_dispatch
name: Upload/Download Artifacts

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - name: Create a File
        run: echo "hello-changed" > hello.txt

      - name: Replace Character in env save it o new env and pass to other step
        id: replace-chars
        run: |
          mkdir tey
          mkdir tey/.hidden
          mv hello.txt tey/.hidden/hello.txt

      - name: Upload Artifact
        uses: actions/upload-artifact@v4
        with:
          name: test-12345
          path: tey/.hidden

  download:
    runs-on: ubuntu-latest
    needs: upload
    env:
      pathmodified: ${{needs.upload.outputs.artifact-name}}
    steps:
      - name: Download Artifact
        uses: actions/download-artifact@v4
        with:
          name: test-12345
      
      - name: Print Artifact
        run: |
          cat hello.txt
```

## Environment Variables

In Github Actions every steps is getting executed by it's own shell. It means environment variables are defined in one step might not be able to be transfared to another step. In order to solve this `GITHUB_ENV` is used. You can transfer your envs to this file and it will load yor env variables in the next step.

```shell
echo "pathmodified=$pathmodified" >> $GITHUB_ENV
```

## Outputs

Outputs is also a way to share info between jobs. So it is useful when you only need to transfer an information rather than an artifact. As an example following workflow transfer a information which is called `message` to `second-run` job.

```yaml
---
on: workflow_dispatch
name: Share Info between jobs

jobs:
  first-run:
    runs-on: ubuntu-latest
    outputs:
      message: ${{steps.echo.outputs.message}}
    steps:
      - name: Echo
        id: echo
        run: |-
          echo "message=merhaba" >> $GITHUB_OUTPUT
  
  second-run:
    runs-on: ubuntu-latest
    needs: first-run
    steps:
      - name: Echo
        id: alpha
        run: echo ${{ needs.first-run.outputs.message }}
```
## Pain Points of Outputs

It worked smoothly until i have an experience about failing to transfer an output. I tried everything such as removing the dash from output name or setting output name before supplying them to `GITHUB_OUTPUT`. None of them worked until i have figured it our from documentation [Github Actions Outputs](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs#overview).

At the end if your output part has an secret it is masked by runner cannot not passed to outputs. In case of having a secret like `12345` you will not able to transfer outputs such as `test12345`. This behaviour is obviously designed for protection of the secrets so inorder to not getting blocked by a situation like this make sure that you are not using common words in your secrets such as `linux`or `arm`.