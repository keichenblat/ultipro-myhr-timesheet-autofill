# Autofill for Timesheet
#### STEP 1: Open your myHR timesheet
#### STEP 2: Bring up the Chrome DevTools (Cmd+Option+I)
It looks like this:

![Screen Shot 2022-06-14 at 12 07 02](https://user-images.githubusercontent.com/67231179/173540070-5bf73763-1679-485e-83e0-40ae977a8d0a.png)

#### STEP 3: Select "main_frame" in the dropdown
![image](https://user-images.githubusercontent.com/67231179/173539881-9a496956-c917-4f41-b137-1f26f6dc715b.png)

### STEP 4: Paste this line into the console and press ENTER
```js
var script = document.createElement('script'); script.type = 'text/javascript'; script.src = 'https://cdn.jsdelivr.net/gh/keichenblat/ultipro-myhr-timesheet-autofill/autofill.js'; document.head.appendChild(script);
```
