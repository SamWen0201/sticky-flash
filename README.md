# Sticky Flash

## Description

A tool that helps people add thoughts or reminders on the notes. Aims to make the add notes process as quickly as possible.

## Demo

[Demo](https://sticky-flash.netlify.app/)

## Tech Stack

![Vue.js](https://img.shields.io/badge/vuejs-%2335495e.svg?style=for-the-badge&logo=vuedotjs&logoColor=%234FC08D) ![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)

- Vue.js for building a reactive web application
- Axios for handling AJAX
- HTML, CSS, Sass, JavaScript
- Easy Error handling when http request failed
- Vite as the build tool

## Background

Suddenly thoughts come up to my mind often. So I wish I could note them immediately. The app doesn't aim to record detailed plan, instead it helps you note an idea. In my experience, the word is less than 20 words often, but I am not sure I would modify the word max capacity in the future.

When I note a thought, I will return to focus on doing things.Maybe 2 days later, I see a note on my desk. And I will then make the thought complete and throw the note in the trash can. That's why I build the app.

## Features

- Press Add a Note button and write something less than(15 words) on the note
- Click the Note, you could edit the Note content
- Click the left corner of the note which will trigger a animation and note will be deleted
- Drag a note to anywhere in the window, and the position will be stored in the database

## Running on Local Machine

After git clone repo links, in the terminal

```
cd STICKY-FLASH
```

download the dependencies

```
npm install
```

running on localhost port

```
npm run dev
```

create a `.env` file in the root directory

```
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

## Future

I would like to add some features which are useful and interesting in the future.

- replace the note content input element with textarea element
- add more color on notes
