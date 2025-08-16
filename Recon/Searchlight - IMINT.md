# Searchlight - IMINT

## Task 1 - Welcome to the Searchlight IMINT room!

The Flag format is `sl{}`

### Answer the questions below

- Did you understand the flag format?

    `sl{ready}`

## Task 2 - Your first challenge!

### Answer the questions below

- What is the name of the street where this image was taken?

    `sl{carnaby street}`

## Task 3 - Just Google it!

### Answer the questions below

- Which city is the tube station located in?

    `sl{london}`

Download the image and search for it

- Which tube station do these stairs lead to?

    `sl{piccadilly circus}`

This name is partially visible in the picture so I searched through all the tube stations listing to find this. The remaining questions were achieved by simple google search.

- Which year did this station open?

    `sl{1906}`

- How many platforms are there in this station?

    `sl{4}`

## Task 4 - Keep at it!

### Answer the questions below

- Which building is this photo taken in?

    `sl{vancouver international airport}`

- Which country is this building located in?

    `sl{canada}`

- Which city is this building located in?

    `sl{richmond}`

## Task 5 - Coffee and a light lunch

### Answer the questions below

- Which city is this coffee shop located in?

    `sl{blairgowrie}`

I searched for the Edinburgh Woolen Mill in Scotland from the picture and searched for the branch with similar looking picture. Upon inspecting the location on google map a shop was visible known as Wee Coffee Shop

![Wee Coffee Shop](/IMAGES/IMINT/IMINT-Weecoffe.png)

- Which street is this coffee shop located in?

    `sl{allan street}`

- What is their phone number?

    `sl{+447878 839128}`

- What is their email address?

    `sl{theweecoffeeshop@aol.com}`

- What is the surname of the owners?

    `sl{cochrane}`

## Task 6 - Reverse your thinking

### Answer the questions below

- Which restaurant was this picture taken at?

    `sl{katz's deli}`

This was easy I discovered it by uploading the image directly to google image search. You can also use [Yandex](https://yandex.com/images/) or Bing for it.

- What is the name of the Bon Appétit editor that worked 24 hours at this restaurant?

    `sl{andrew knowlton}`

Searched this question with the image search and got the answer

## Task 7 - Locate this sculpture

### Answer the questions below

- What is the name of this statue?

    `sl{rudolph the chrome nosed reindeer}`

- Who took this image?

    `sl{kjersti stensrud}`


## Task 8 - ...and justice for all

### Answer the questions below

-   What is the name of the character that the statue depicts?

    `sl{Lady Justice}`

- Where is this statue located?

    `sl{Alexandria, Virginia}`

This question was a tough one but I found out after using image in google search through an article having **Albert V bryan Courthouse** name visible in the picture. 

![Albert V Bryan Courthouse](/IMAGES/IMINT/IMINT-Location(V.%20Bryan%20Courthouse).png)

Searching this name gave away the answer as **Alexandria, Virginia**

- What is the name of the building opposite from this statue?

    `sl{the westin alexandria old town}`

Utilizing the above search of Albert V Bryan Courthouse I looked up the map for the place and found out the answer to this question

![the westin alexandria old town](/IMAGES/IMINT/IMINT-WestinAlexandriaOldtown.png)


## Task 9 - The view from my hotel room

Here we were given a video file which we had to search through.

A tool called **ffmpeg** was also mentioned as a resource which I downloaded through the [github repo](https://github.com/BtbN/FFmpeg-Builds/releases). After installation I referenced this [video](https://youtu.be/qjtmgCb8NcE?si=8s9lD1g2oKNrAeLa) for the setup.

After setup go to the folder where your video file is and type this command in cmd: `ffmpeg -i myvideo.mp4 -r 1 img%06d.png -hide_banner`. The video will break down into frames making it easy to recognize any fruitful information.

However we can also do the recon without this tool easily by playing the video.

From the obtained frames I got a place called **Riverside Point** which I searched through google maps and found out there is place called **CLark Quay** in front of it but no visible hotels. Upon opening Clark Quay (I searched through writeups and got the hint that the hotel used to be here) in google street view I found an image of **novotel singapore clarke quay hotel** which was the answer to the question.

![novotel singapore clarke quay](/IMAGES/IMINT/IMINT-ClarkQuay.png)

### Answer the questions below

`sl{novotel singapore clarke quay}`

