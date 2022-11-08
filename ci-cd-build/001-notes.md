## General


> __*Do I need a CI/CD/Pipeline?*__ 
>
> Yes

> __*Aren't they very complex to setup?*__
>
> The can be become extensive, and can seem daunting. But they should be composed of simple elements. If any part of the implementation of the pipeline is complex - then it is wrong. 


### Key Experiences

- Keep it simple, straightforward, and consistent.
    - If all goes well the CI pipeline will sit there for ages doing it's thing and you want need to touch it. 6 months later somebody will need to make a change, and they need to be able to do this easily. You need to be able to read and understand a pipeline easily and quickly. You don't want to have figure out 5 levels of indirection, with dynamically created shell scripts, with custom post processing tools all in a mixture of Python, JavaScript, sed/awk and bash
    - A lot of pipelines have a high degree of repetition; try and keep similar approaches to do similar things. And where possible try and reuse components. But avoid being tempted to go too far with parameterising, or adding special cases. "Do one thing and do it well"
    
- Use the platform features - especially true when migrating don't try and replicate the old system in the new one exactly.  Don't fight the platform!

- Error Cases
    - Always, always check things have worked and take action if they haven't. Especially true of publishing or push to production pipelines. Things that are very hard to test out of context. 
    - Pipelines aren't run in a a two phase commit transaction - if for example the npm publish works, but pushing the docker image to a registry fails - what does this mean?  Can you respin the docker image publishing but take account of the already published node module?
    - Pipelines should be idempotent when it comes to publishing.

- Debug output
    - You will probably debug a pipeline more through the logs than anything else; make sure the output is clear and contains sufficient information. 
    - It's easier to filter out unrequired information than re-run a failing pipeline with more debug

- Try and make replication of the pipeline locally possible. 
    - dagger.io is a good step towards making this easier - and is worth looking at.
    - If there is a pipeline issue that is hard to handle, or indeed when you're building locally try and be able to run the same commands in teh same sequence as actual pipeline runs. 
    - This will give you a greater confidence in the pipeline.


### Technology Choices

There are many platforms that will run pipelines, they are all achieve broadly the same goal. In the fact they achieve the same goal, they do have strong similarities to each other. Much the same way that one high-level language has similarities to others.

#### Github Actions

Very good choice if the code is already in github,  GHA is well connected in to working with the code repository. You see this primarily when dealing with secrets for pulling code and pushing to repositories. The secrets needed are already pre-populated in the running environment. 

It can also be managed via the `github cli` a feature missing from some other platforms.

#### Azure Pipelines

Strong platform and was the preferred platform of choice prior to moving to GHA. It had added features that let test results, artifacts, code coverage stats etc be pushed to the build result. This is common to other platforms, but Azure Pipelines had a very good interface. 

THis was out platform of choice prior to GHA; though whether it will remain now GHA is available is a question. However the syntax between the two is very very similar so migration should not be an issue. 

#### Travis-CI

A few earlier projects used this and it was effective and did it's work well. However we found the performance at the time to be poor, and a better deal was to be had with Azure. 

#### Tekton

A different approach, and best suited to pipelines that are one-step away from the repo-level pipelines. For example if you want to do integration testing across several components. Longer test run orchestration for example. We've used this very effectively for this - and I would certainly look to use it again.  It's less useful for repo-level pipelines - there isn't a 'setup nodejs' or 'extract code from git' built-in task/action.

Helps to have more of a knowledge of K8S, and it's configuration is more 'verbose' that others.  

Some cloud vendors offer their own hosted Tekton based CI systems, worth looking at some added some good features. But as with any vendor hosted open solution it can sometimes be hard to see where the open solution ends and the hosted solution starts.  





