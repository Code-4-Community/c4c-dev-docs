# Add Yourself to the People Page

This is a quick guide on how to add yourself to the [people page](https://c4cneu.com/people)!


## Step 1: Get a nice picture!

Go to [https://c4cneu.com/people](https://c4cneu.com/people) to see examples of what the photos are supposed to look like. Crop a **square** picture of yourself at a similar scale/perspective to the other photos on the page with dimensions around 500 x 500px.

Example:

<img src="https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/Jack-B.jpg" style="display: block; height: 250px; margin: auto;">

You can actually view or save this photo to compare to a photo on your local machine!

## Step 2: Upload the picture to AWS.

Use your AWS credentials (if you dont have one, ask for one!) to upload your photo to the `c4cneu-public` bucket, under 'Board/2020'.

Once you have placed the image in the correct folder in the correct bucket, make sure that you set your image to be **publicly accessible**. This will allow you to access a URL to that image without permissions.

**Copy the URL to your image on AWS, we're going to need it later!**


## Step 3: Clone the C4C website.

Go ahead and `git clone https://github.com/Code-4-Community/c4cneu.com.git`. (For more information see the repo [here](https://github.com/Code-4-Community/c4cneu.com)
)

Run `npm install`, then `npm start`, a new tab should pop up with a locally running version of our website!

## Step 4: Modify the people page.

Using your code editor of choice, navigate to `c4cneu.com/src/pages/people/PeopleData.tsx`.

You should see *something* like this:

```js
export interface Person {
  readonly name: string;
  readonly position: string;
  readonly image: string;
  readonly linkedin: string;
  readonly email: string;
}

export const people: Person[] = [
  {
    name: 'Liam Moynihan',
    position: 'President',
    image:
      'https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/Liam-M.jpg',
    linkedin: 'https://www.linkedin.com/in/liam-moynihan-b75806123/',
    email: 'moynihan.li@northeastern.edu',
  },

  ...

  {
    name: 'Justin Konecny',
    position: 'Technical Lead',
    image:
      'https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/Justin-K.jpg',
    linkedin: 'https://www.linkedin.com/in/justin-konecny/',
    email: 'konecny.j@northeastern.edu',
  },
];

```

Go head and add your information here. For the image string **use the URL to the image you uploaded to AWS in step 2**.


The file should now look like:

```js
export interface Person {
  readonly name: string;
  readonly position: string;
  readonly image: string;
  readonly linkedin: string;
  readonly email: string;
}

export const people: Person[] = [
  {
    name: 'Liam Moynihan',
    position: 'President',
    image:
      'https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/Liam-M.jpg',
    linkedin: 'https://www.linkedin.com/in/liam-moynihan-b75806123/',
    email: 'moynihan.li@northeastern.edu',
  },

  ...

  {
    name: 'Justin Konecny',
    position: 'Technical Lead',
    image:
      'https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/Justin-K.jpg',
    linkedin: 'https://www.linkedin.com/in/justin-konecny/',
    email: 'konecny.j@northeastern.edu',
  },
  {
    name: 'Your Name Here',
    position: 'Your Role at C4C',
    image:
      'https://c4cneu-public.s3.us-east-2.amazonaws.com/Board/2020/First-L.jpg',
    linkedin: 'https://www.linkedin.com/in/your-linkedin-handle',
    email: 'last.f@northeastern.edu',
  },
];

```

Save the file, and wait for changes to appear.

## Step 5: Check your work.

If you don't already have the frontend running while you make changes, run `npm start` again. Navigate to the people page and make sure your picture looks good on the site!


(Optional)
**Take a screenshot of the people page with you in it**, we'll use it in our PR!


## Step 6: Make a pull request.


We now need to switch branches, in the root directory of the frontend, type `git checkout -b firstlast_add_to_people_page`.

For example, I might write `git checkout -b ryanjung_add_to_people_page`.

The modifed files, which should only be `PeopleData.tsx`, commit, and push them to your branch.

Then go to [https://github.com/Code-4-Community/c4cneu.com](https://github.com/Code-4-Community/c4cneu.com) and make your branch into a pull request.

In your pull request paste in a screenshot of the people page with your new photo on it!

Finally, remember to ask for a reviewer. For simplicity you can assign me (Ryan Jung).


...and thats it! Congratulations, you're on our website!