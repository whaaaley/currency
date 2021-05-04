
# Currency Exchange (Code Challenge)

## Prioritize Requirements
1. As a user, I should see a list of currencies that I can convert my money from.
1. As a user, I should be able to select 1 or more output currencies.
1. As a user, I should be able to submit a value for the amount I want to convert.
1. As a user when I submit my selections, I should see the different output exchange rates with information that indicates what my best exchange rate is.
1. As a user, I should be able to persist my results to a database and access by my email address. Do not build auth for the persistence. Pick a naive solution and explain your choice.

## Rationale

I spent some time early on researching Docker and Laravel. I've known of these projects for a long time but never used them myself as I'm primarily front end. Most of the backend projects I've done were in Node. After spending some time reading and installing Docker and the Laravel example app, I moved on to work on the front end. I used [Vite](https://github.com/vitejs/vite) to get a Vue 3 project up and running super quick. I was able to get most of the frontend requirements done in the first few hours.

On day 2, I started working on the rest of the SWOP API and was able to wrap everything up pretty quickly. I also added a quick local storage persistence for the currency list as I was afraid of hitting the monthly request limit.

After finishing the frontend I went back to work on setting up Laravel, but was much more involved than I expected. I was struggling to get a blank Docker container setup at all. I planned to get the container up and use Jetstream as a base since it already has auth and routing. I think I could learn Docker and Laravel over time if necessary, but in the short period I had left to complete this challenge I wasn't able to.

## Timeline:

5/3/2021
 - Start time 9:30 AM
 - Installed docker + Researched Laravel
 - Setup + Worked on frontend
 - End time 3:30 PM

5/4/2021
 - Start time 7:30 AM
 - Finished frontend + API stuff 9:30 AM
 - Research Laravel + Docker
 - End time 11:00 AM
