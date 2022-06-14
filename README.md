# Autofill for Timesheet
### How to
#### STEP 1: Open your timesheet
#### STEP 2: Bring up the Chrome DevTools (Cmd+Option+I)
![Screen Shot 2022-06-14 at 12 07 02](https://user-images.githubusercontent.com/67231179/173540070-5bf73763-1679-485e-83e0-40ae977a8d0a.png)

#### STEP 3: Select "main_frame" in the dropdown
![image](https://user-images.githubusercontent.com/67231179/173539881-9a496956-c917-4f41-b137-1f26f6dc715b.png)

### STEP 4: Paste this code into the console and press ENTER
```js
if (!$('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3)')) {
  console.log('==== Auto-Fill error ====');
  console.log('Cannot fill hours: You must choose the correct frame in the selector under the "Console" tab (it is called "main_frame")');
  console.log('=========================');
} else {
  const validWorkTypeStrings = ['home', 'office'];

  let workTypeString = prompt("Did you mostly work from 'home' or 'office' this month? Type either 'home' or 'office'");
  while (!validWorkTypeStrings.includes(workTypeString.toLowerCase())) {
     workTypeString = prompt("You must type either 'home' or 'office':");
  }

  const WORK_DAYS = [0, 1, 2, 3, 4];    // use 1, 2, 3, 4, 5 outside of Israel
  const DAY_START = "9:00";
  const DAY_END = "18:00";
  const WFH_HOURS = 8.4;                // Number of WFH hours
  const WORK_TYPE = workTypeString.toLowerCase() === 'home' ? 8 : 5;                  // 5 - Work from office | 8 - Work from Home

  // TODO: Try to add some interaction with the user to allow them to choose "work type" and maybe even fill in days off

  $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3)')[0].click();
  const EXISTING_ENTRIES = $('[id^=gdvTS_rw_]').toArray().filter((e)=> /_TPDATE$/.test(e.id)).map((e)=> (e.title.match(/(?<month>\d+)\/(?<day>\d+)\/(?<year>\d+)$/) || {}).groups).filter(Boolean).map(({ month, day, year })=> new Date(Number(year), Number(month) - 1, Number(day)).getTime());

  $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3) > select > option')
    .map((a, c)=> $(c).val())
    .toArray()
    .slice(1)
    .filter((date)=> { 
      const { day: monthDay, year, month } = (date.match(/^(?<month>\d+)\/(?<day>\d+)\/(?<year>\d+)$/) || []).groups;
      const entryDate = new Date(Number(year), Number(month) - 1, Number(monthDay));
      return !EXISTING_ENTRIES.includes(entryDate.getTime()) && WORK_DAYS.includes(entryDate.getDay());
    })
    .flatMap((day)=>[
      ()=> $('a#ImgAddRow').click(),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3)')[0].click(),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3) > select > option:nth-child(2)').each((a, c)=> $(c).parent().val(day)),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(1)').each((a, c)=> { $(c).click() }),
      ()=> $(`div#scrolledGridView tr:nth-last-child(1) > td:nth-child(1) > select > option:nth-child(${WORK_TYPE})`).each((a, c)=> { $(c).parent().val($(c).val()) }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(2)').each((a, c)=> { $(c).click() }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(2) > input').each((a, c)=> { $(c).val(DAY_START) }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(2) + td').each((a, c)=> { $(c).click() }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(2) + td > input').each((a, c)=> { $(c).val(DAY_END);  }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3) + td').each((a, c)=> { $(c).click() }),
      ()=> $('div#scrolledGridView tr:nth-last-child(1) > td:nth-child(3) + td > input').each((a, c)=> { $(c).val(WFH_HOURS); }),  
    ])
    .reduce((ac, v)=> ac.then(v).then(()=> new Promise((resolve)=> setTimeout(resolve, 10))), Promise.resolve());
  
  console.log('Autofill in progress...');
  setTimeout(() => {
    console.log("[!] Don't forget to manually fill your days off");
  }, 1500);
}
```
