## Final assessment

The goal of this is to assess your skills in programming and associated tasks (version control, DevOps, agile, etc). You will be given 2 programming challenges and some programming related questions. 

1. Comments Challenge
2. Viaplay Challenge
3. Written submission

#### Time & Format
4 hours during which you need to be online in a coach supervised conference call (Google Meet). You need to share your screen/monitor where your IDE is displayed. 

#### Delivery:

All code must be delivered in the form of a **Pull Request** against the repositories that the examiner will provide to you (one for each challenge). Please note that the repositories will be set in **public** mode for the duration of the assessment, and closed sourced once we finish the assessment. We, the coaches of CA will be the only ones that can see them. We undertake this step to protect your privacy. 

## Comments Challenge
You are challenged to implement a feature for the Crafty News API.

Title: "User can comment on an article"

User story:
```
As a User
In order to voice my opinion on an article
I would like to be able to leave a comment
``` 
Tasks:
- [ ] Write request spec
- [ ] Add comment model
- [ ] Comment needs to contain a body
- [ ] Add association between comment and article
- [ ] Only authenticated user can comment, add user model with devise token auth
- [ ] Add association between comment and user


You need to fork the repository and clone the forked repository to your computer. Then you need to branch off from the development branch. When you feel that you are done, you need to create a pull request towards the upstream repository. Make sure that the PR contains the correct information. The user story and what changes that are proposed in the PR.

Link to the upstream repository: https://github.com/CraftAcademy/crafty_news_API_tech_interview

Good luck!

## Viaplay Challenge

You are challenged with the task of replicating a UI. It is a desktop-only web application listing TV series from Viaplay. 

The presented UI looks like this:

![](./viaplay_challenge_ui.png)


You need to fetch the data from the Viaplay API and find the appropriate attributes that hold the information you need. 

The API is located at:

```
https://content.viaplay.se/pc-se/serier/samtliga
```

The TV series listings can be found at: 
```js
yourDataObject._embedded['viaplay:blocks'][0]._embedded['viaplay:products']
```

You can find a logo with a transparent background at: 
```
https://kundservice.viaplay.se/wp-content/themes/viaplaycs/assets/dist/images/viaplay_white.svg
```

Displaying the shows in a row with the right formatting is a bit challenging. To help you, we've chosen to provide an example solution that is proven to yield good results. It is up to you if you want to use it though. The rest of the css needed to replicate the UI is up to you to define.

Link to the upstream repository: https://github.com/CraftAcademy/final_exam_viaplay_challenge

```css
.display-show {
    display: inline-block;
    width: 23%;
    width: calc(25% - ( ( 20px ) * 0.75 ) );
    margin-right: 20px;
    margin-bottom: 20px;
}

.display-show:nth-child(4n+4) {
    margin-right: 0;
}

.display-show img {
    width: 100%;
    height: auto;
}
```

Good luck and Happy Coding. 

## Written submission
Please explain the steps you think are necessary to perform to get a feature done, from an idea to an implemented solution that is running in production. Be as detailed as possible. 