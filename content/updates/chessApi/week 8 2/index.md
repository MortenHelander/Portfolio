---
title: "Week 8"
date: 2026-10-07
draft: false
project: "chessapi"
header: "WeekEightPartTwo"
externalUrl: "Blog/chessAPI/#weekeightparttwo"
summary: "Security: JWT and BCrypt"
---


## Security: JWT and BCrypt

This week we've looked at how to implement better security in our API's. We first looked at BCrypt, which we've used before but haven't really been explained in details how it works (or maybe I just didn't get it back then). It was a big eye opener on how important your own password actually is, because even with encryption libraries such as BCrypt our teacher told us hackers had lists of the most easy and common used passwords aldready encrypted. It's also interesting that it deliberatly encrypts very slow, as to make hackers job more difficult. How the salt works I'm a bit uncertain but the process is a lot less "magic" than on last semester.
Where users might make less prudent decisions in chosing a secure password, we as the developer can add an extra layer of security in form of JWT (JSON web token), which serves as both an extra layer of security but also a smart tool for keeping a user verified instead of having to type your credentials for each new request.
Implementing the BCrypt was very easy to do, it's only a few lines, but I decided to expand a bit and added some better validation for registrering as a user in general which was a nice addition. The JWT however was much more difficult, since it's the first working with this technology I ended up already spending about 3-4 times longer than I expected and what I first thought was gonna be a one day coding session, is probably gonna last me a few days. Luckily we don't have anything else than time to work on our project next week.
All in all, a bit wiser in IT-security but also more knowledgeable that it's an enourmous topic and I'm beginning to understand how people can make entire careers or even companies with this as their niche.