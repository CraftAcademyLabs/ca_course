Now when we are done with the logic and the layout for the Address Book app we want to add some styling

We will be using [Tailwind CSS](https://tailwindcss.com/) for the styling. 
Tailwind CSS is a styling framework used for rapidly getting CSS to an application.  The first thing we need to do is to add a CDN with the stylesheet in the head tag. 

```html
<head>
	<meta  charset="UTF-8">
	<meta  name="viewport"  content="width=device-width, initial-scale=1.0">
	<meta  http-equiv="X-UA-Compatible"  content="ie=edge">
	<link  href="https://cdn.jsdelivr.net/npm/tailwindcss/dist/tailwind.min.css"  rel="stylesheet">
	<title>Address book</title>
</head>
```

After we have addeed the stylesheet CDN we are ready to start styling our app. 

With frameworks like tailwind we are provided with CSS connected to class names. This means that we can easily add styles to whatever html tag with just adding a class. 

The tailwind [webiste](https://tailwindcss.com/docs/what-is-tailwind) has  lot of reference colors and classes that we can use.

Make sure that you make your site presentable.

Once you have added styling to your page your next task is to deploy your site to Github pages. Instructions for deployment is in the prep course. 
