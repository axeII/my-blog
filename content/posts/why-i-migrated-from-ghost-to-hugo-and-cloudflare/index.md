---
title: Why I Migrated from Ghost to Hugo and Cloudflare
date: 2023-12-23
author: Akira
slag: why-i-migrated-from-ghost-to-hugo-and-cloudflare
tags: [Ghost, Hugo, Cloudflare]

showHero: false
---

# Introduction

In this post, I will share my experience and reasons for migrating my blog from the Ghost platform to Hugo and Cloudflare. I will discuss the benefits, challenges, and steps involved in the migration process. It's been a while I migrated my blog from Ghost to Hugo and Cloudflare. I wanted to share my experience and the reasons behind this decision. I hope this post will be helpful for others who are considering a similar migration.

## Why Migrate?

So why the migrate? There are several benefits that led me to switch from Ghost to Hugo (and Cloudflare):

* Cost
* Performance
* Easier to maintain
* Automation

Now let's dive into each of these reasons in more detail. First up, cost.

### Cost

Before I moved my blog to cloudflare I was using Ghost. Since ghost needs vm to run (since ghost is running on nodejs) I had to run my blogs on VPS (virtual private server). I tried many platforms from Azure, AWS, GCP and finally Oracle cloud. But when Oracle started automatically shutting down my VMs I decided to move to move elsewhere. My goal was why to go totally free forever. And this is my first reason to moving to hugo since static page can be hosted many free providers like Cloudflare Pages. But I will talk more Cloudflare later.

### Performance

Honestly I didn't have any performance issues with Ghost. But I wanted to try something new. I wanted to try something that was faster and more efficient. Hugo is a static site generator that generates static HTML files. This means that the site is faster to load and more efficient to serve. This was a big reason for me to switch to Hugo. I wanted to see how much faster my blog could be with a static site generator. Also mobile perfoance was kind of issue on Ghost. I wanted to see if I could improve the mobile performance of my blog with Hugo.

### Easier to Maintain

Another great thing about Hugo is that it's super easy to maintain. Since it's a static site generator, there are no databases to manage or servers to maintain. This makes it much easier to update and maintain the blog. I don't have to worry about security updates or server maintenance. I can focus on creating content and not worry about the technical details. This was a big reason for me to switch to Hugo. I wanted to spend less time managing the blog, creating backups, managing the vm, and more time creating content.

### Automation

My final reason for switching to Hugo was automation. I wanted to automate the deployment process of my blog. I wanted to be able to push a commit to my repository and have the changes automatically deployed to the live site. This is something that is much easier to do with a static site generator like Hugo. I wanted to use GitOps practices to automate the deployment process. This was a big reason for me to switch to Hugo. Deploying my stuff and theme update was never easier. This was also big plus since my previouse Ghost theme was difficult to maintain and update.


## Content Migration

Now let's say your are considering to move to the Hugo right now. Your next question should be how to migrate the data from Ghost to Hugo. There are several ways to do this. You can export the data from Ghost and import it into Hugo. It can be simple but it really depends how much data do you have. Technically both Ghost and Hugo are using markdown files so it should be easy to migrate the data. But there are some things that need more attention. First the structure of the markdown files and the way Ghost hosts the data. Second the images and other media files. And finally the metadata of the posts.

There are tools to automate this. The first thing I did was to export the data from ghost to json file and then started googling whicih tool I could use to generate the markdfiles for me. I think I used this tool [ghostToHugo](https://github.com/jbarone/ghostToHugo). This is a simple command line tool that converts Ghost JSON exports to Hugo markdown files. It's super easy to use. You just need to export the data from Ghost and run the tool. It will convert the data to Hugo markdown files. You can then import the markdown files into Hugo. It's a simple and easy way to migrate the data from Ghost to Hugo.

If I have to be honest I had to do some manual work to fix some of the posts. But it was not that much work. I had to fix some of the images and metadata. I think it took me a couple of hours to migrate all the data from Ghost to Hugo. But that really depends how much posts you have to migrate. For me it wasn't that hard. Still it took some trial and error to get everything right.

I think this might be for some people the breaking point to move to Hugo. But I think it's worth it. You don't really need that command line and yaml skill to do this.

## Theme Customization

For my new hugo blog I went again for theme hunting. I found a theme that was close to what I wanted. I then customized it to match my blog's design and branding. One of the things I really do like about hugo is how much you can actually customize the theme. At least this was my case when I started using the blowfish. Blowfish is a hugo theme made by Nuno Coração. The theme can be found [here](https://github.com/nunocoracao/blowfish). It's free and it's open source.

Adding this theme was super easy. I just followed the guide which was just to git clone this project to `themes` folder in my hugo project. I had to addid to the git submodule settings since I wated to make it easy to deploy using git. Then I just had to add the theme to my `config.toml` file.

```toml
baseURL = 'https://axell.dev/'
theme = "blowfish"
languageCode = 'en-us'
...
```

There is just so many other things and I think that Nuno did a great job about this documentation, so I will just post the link to the documentation [here](https://blowfish.page). Super simple just follow the guide.


## Performance Optimization

Now that I had my hugo blog ready with the desine I liked I needed a way to simply deployit or even better automate the deployment. As a DevOps engineer I like to automate things but also use practices like GitOps. The flow is simply to update your code you push things to github and wait for the pipeline to automatically deploy the changes. This is what I wanted to do with my blog.

There are many more option for this so if you are reading this this the way I did this but it's definately no the only way since hugo generates static html files you can deployit alsomost everywhere you want.

1. **GitHub Pages**
   - Free for public repositories and easy to integrate with GitHub.
   - Supports custom domains.

2. **Netlify**
   - Free tier available.
   - Supports continuous deployment from Git repositories.
   - Custom domain support.

3. **Vercel**
   - Free tier available.
   - Supports deployments from Git repositories.
   - Custom domain support.

4. **Firebase Hosting**
   - Free tier available.
   - Custom domain support.

5. **Amazon S3 with CloudFront**
   - Pay-as-you-go model.
   - Highly customizable with global delivery via CloudFront.

6. **Render**
   - Offers a free tier with a simple deployment process.
   - Custom domain support.

7. **Surge.sh**
   - Easy-to-use CLI tool.
   - Free tier allows basic sites with limited custom domain support.

Each of these services has different features and pricing models, so you can choose the one that best fits your needs. So there are quiet lot of choices today to choose from. Pick your own posion. 😇

Anyway my choice was Cloudflare since I am already using it for other projects - making it an easy choice for my needs. Cloudflare pages was super easy to setup.

I visited cloudflare.com. Found the pages settings under "Workers and Pages" on the left bar. Clicked on create new project.
![fresh look](images/workpages.png "Click on create new project")

From there I clicked on "Pages" since all we need is pages settings. Here I had to login with the github account. The dialog will ask you which repository you want to use with your account. If you don't see the repository there then you need to firt all cloudflare to give access to your repositories. You can choose one specific repository.

![repository](images/click.png "Choose the repository button that will guide you to the next step")

All that remains is to set up the build process, as Cloudflare should already have access to your repository. You need to configure the build command to run the Hugo build.

```bash
hugo --minify -t blowfish
```
and set the output directory to `public`.


![cloudflare](images/cloudflare.png "Build hugo settings")

Finally, set your custom domain (in my case, it was axell.dev). This was straightforward, maybe because I also use Cloudflare for the domain's DNS. And that's it. Once I push a commit to my repository, Cloudflare will automatically build and deploy the changes to my blog. This is a great way to automate the deployment process and keep the blog up-to-date with the latest changes.

## Conclusion

I am really happy with my decision to migrate my blog from Ghost and Oracle cloud to Hugo and Cloudflare. This migration was chalanging and it had moments where I was stuck but in the end all the hard work was worth it. When I look back now I see only benefits from this migration, including cost savings, improved performance, easier maintenance, and automation. I am happy with the new design and the performance of the blog on browsers and mobile devices. As a DevOps engineer I feel more comfortable with the way I can deploy my blog now. I can focus on creating content and not worry about the technical details.

I would recommend others to consider a similar migration if they are looking for a faster, more efficient, and easier-to-maintain backups.

You need to be comfortable with editing things by your self. Hugo doesn't have a GUI editor like Ghost. For me this was not a problem, since I use vscode to edit my post I also run local hugo dev server (very simple to install and run) so I can see the changes immediately like in Ghost. And when I am happy with the post I just do git commmit and git push and it's done. Very simple.

Also I think I should mention pluings. There might be plugins you are using on Ghost that you can't use on Hugo. My blogs is supper simple the way I like it so I didn't have to worry about this. Although I lost the comments. I am workig on to fix this but for now my blog doesn't have comments option like it had on Ghost. Well now nobody can critisize my posts so I guess that's also benefit 😂.

But I understand that this might be a huge deal for some people. I remember there was soo much stuff in Ghost which I considered useless for my case but it might be important for you. If that's the case I recommend to check the Hugo documentation and see if there is a process that can do the same thing you are using on Ghost.

I hope this post has been helpful for others who are considering a similar migration. If you have any questions or need help with the migration process, feel free to reach out to me. I would be happy to help. Thank you for reading!

<!-- Summarize your experience with the migration process and the benefits you have achieved by switching to Hugo and Cloudflare. Share any final thoughts or recommendations for others considering a similar migration. -->
