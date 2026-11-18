# Job Search Skill

<img width="1915" height="906" alt="image" src="https://github.com/user-attachments/assets/d74d76c7-9dfd-4328-8ed2-1fff5cf98a07" />

An agent skill that searches job sites for you. You fill in a short settings table (job titles, skills, locations, sites) or point it to your CV. It searches the sites you choose, rates every listing against your profile, and keeps all results in one local list that you browse in a web page.

- Searches any job site you configure, plus Google discovery and optional job-search MCP servers
- Checks each new job against your core keywords before keeping it
- Rates each job: Very strong, Strong, Possible or Weak fit, with matches, gaps and caveats
- Marks senior roles
- Never adds the same job twice, so every run only adds new jobs
- A local page with filters, search, sorting, paging, a "Show only new jobs" filter and a saved "Applied" mark
- Light and dark theme
- Everything stays on your computer

## Install

1. Download the ZIP from the [GitHub Releases page](https://github.com/Hazy963/job-search-assistant-skill/releases) and extract it, or clone this repository.
2. Install the `job-search` folder (it contains `SKILL.md` and `index.html`) as a skill:
   - **Claude Code:** copy `job-search` to `~/.claude/skills/` (all projects) or `.claude/skills/` (one project).
   - **Claude app:** zip the `job-search` folder and add it in the Skills settings.
   - **GitHub Copilot / other agents:** place the folder where your agent loads skills. If it has no skill support, paste the content of `SKILL.md` as the prompt and give the agent access to the folder.
3. Make sure the agent has a browser tool (for example the Playwright MCP server) and permission to read and write your job-search folder.

## Requirements

- An AI agent that supports `SKILL.md` skills and can **use a browser and write files on your computer** (for example Claude Code, Claude Desktop or Cowork, or GitHub Copilot with the Playwright MCP server). A plain web chat cannot write to your disk.
- GitHub Copilot's free plan works: it supports Playwright MCP for browsing.
- Microsoft Edge or Google Chrome to open the results page (it uses their folder-access feature). Firefox is not supported.

## Set up your settings

Your settings are stored in `settings.md` inside your job-search folder (default `~/job-search`). The agent creates it on the first run and asks for anything missing, so you don't have to edit anything first. You can change `settings.md` at any time, or tell the agent to change a setting.

Optional: before installing, you can fill in the table at the top of `SKILL.md`. Those values become the starting values in `settings.md`. After the first run, `SKILL.md` is no longer used for your settings, so updating the skill never overwrites them.

| Setting | Meaning |
| --- | --- |
| Base folder | Default `~/job-search`. Set it in the `SKILL.md` table before the first run, or name it in your message. It is also copied to `settings.md` as a record, but changing it there has no effect |
| Job titles / search terms | What to search for |
| Skills (or CV path / link), experience years, seniority, qualifications, languages | Used to rate every job. Skills not listed are never assumed |
| Core keywords | A job is kept only if it names one of these in the title or must-have requirements ("nice to have" does not count) |
| Exclude keywords | Jobs mentioning these are skipped |
| Locations | City and radius in km |
| Senior handling | `separate` (default, Senior tag plus stretch check), `exclude` or `include` |
| Job sites | The sites to search (see below) |

## How a run works

1. **Start.** Say "find jobs" or "run the job search" (or invoke the skill with `/` and its name, then `run`).
2. **Read the settings.** The agent reads `settings.md` and `profile.md` from your job-search folder.
3. **Ask only if needed.**
   - If a required row is empty (job titles, skills, locations, job sites), the agent asks for all missing items in **one message** and saves the answers in `settings.md`.
   - If you gave a CV, the agent reads it once, shows you a short summary to confirm, and saves it as `profile.md`. Only job titles, skills, experience, qualifications and languages are kept. Your name, phone, email, address and the CV text are **not** stored. The CV is only read again if you change the CV path or ask for a refresh.
   - If everything is filled in, it asks nothing.
4. **Search.**
   - It creates a lock (`run.lock`) so two runs cannot overlap.
   - It searches each site with the browser. For Arbeitsagentur it tries the MCP server first and falls back to the browser.
   - It skips jobs it has already seen.
   - It opens each new job and checks that a core keyword is in the title or must-have requirements.
   - It rates each job against your skills and marks senior roles.
5. **Save and report.**
   - Only new jobs are added to `jobs.json`.
   - The agent tells you how many are new and whether any site failed or was blocked.
6. **View the results.** Open `index.html` with Edge or Chrome and click **Reload data**.

## GitHub Copilot integration example
Here you can find a full example of how to install, run and present report values to track which jobs are newly found and which ones you have or have not applied to yet.

### Skill configuration
Configure your skill before installing it to make working with it easier. Here you define the folder where your reports will be saved, your job title, and the MCP tools and websites to use. The values you enter here are copied into `settings.md` on the first run.
NOTE: Base Folder will always be read from the skill md or your message so be sure to configure it hiere before install.

<img width="860" height="853" alt="image" src="https://github.com/user-attachments/assets/29b67b19-5ac3-437c-9064-e56cc93bc1b9" />

### Installing the skill
Go to customization, click the green Add button, then Skill, and click Install:
<img width="1917" height="247" alt="image" src="https://github.com/user-attachments/assets/3b5d9496-4e22-4ee4-9344-4515c8734203" />

Choose the extracted skill folder where you configured your skill properties. You should end up with something like this:
<img width="1168" height="662" alt="image" src="https://github.com/user-attachments/assets/cffe6a89-5afc-44db-aa27-57a16804aedf" />

### Run the skill:
you can just call the skill and say run it will use your configured information or ask you for missing ones and copy them to setting.md
<img width="1392" height="898" alt="image" src="https://github.com/user-attachments/assets/7d72377f-4495-4093-951d-44fd5a032b99" />
### Presenting Reports Result:
if you have the "Open page after run" flag set to true the skill will automaticlly opens the index.html and you can select your defined base folder to see all founded jobs of all runs and have the abilty to filter them and mark them as appield or leave them to apply to them later

## Everyday use

1. Ask: **"Run the job search."**
2. Open `index.html` in your job-search folder.
3. First time only: click **Select job folder** and choose the folder that contains `index.html`. The browser remembers it. If you move the folder, use **Change folder**.
4. After each run, click **Reload data**.
5. To change settings, edit `settings.md` or tell the agent ("add Munich, 30 km").

On the page you can search, filter by fit, level, city and source, sort, and tick **Applied** (applied jobs are hidden by default; use **Only applied** to see them). **Show only new jobs** shows what the latest run found and combines with all other filters.

## Job sites

Each site in the settings table is one of three types:

- **Website:** any job site, searched with the browser. If a site's URL pattern stops working, the agent falls back to the site's own search box. Remove or disable a site to switch it off.
- **Google:** finds listings on other boards and company career pages.
- **MCP:** an MCP server with a job-search tool, for example [JobsucheMcp](https://github.com/Hazy963/JobsucheMcp) for the German Bundesagentur für Arbeit. These are API results without page numbers. If the MCP is unavailable, the agent falls back to the browser.

Job sites change their URLs, so check patterns in your browser and adjust.

## Files

```
your-job-search-folder/
  settings.md          your settings (created on the first run)
  profile.md           your confirmed profile summary (from your CV or skills)
  index.html           the results page
  run.lock             exists only while a run is in progress
  data sources/
    jobs.json          all found jobs, one file
    excluded.json      skipped, not-relevant listings
    applied.json       your Applied marks (written by the page)
```

Duplicates are avoided from `jobs.json` and `excluded.json`: a job is skipped if its URL or ID is already stored, or if company, title and city match. Existing entries are never changed. You can back up or move the folder like any other.

## Privacy and fair use

- Results, settings and Applied marks stay on your computer. Nothing is uploaded by the skill itself. Your agent provider sees what the agent reads, such as job pages and the CV you share.
- Check each job site's terms before automating it. Use the skill for personal job hunting, keep runs modest, and do not try to bypass logins or CAPTCHAs. The skill reports a blocked site instead of working around it.
- Job availability can change. Always check the original listing before applying.

## Troubleshooting

- **The page shows no jobs:** select the folder that contains `index.html` and `data sources`, then click **Reload data**.
- **A site returns nothing:** it may block automation or have changed its URL. Check the Job sites row in `settings.md`, or remove the site.
- **The page asks for the folder again:** after a browser restart it may ask for permission. Click **Select job folder**.
- **"Another run is in progress":** a run is still active. A lock older than 3 hours is treated as stale and replaced automatically.
- **The agent asks all questions again:** it can't find your folder or `settings.md`. Tell it the folder path, or check that `settings.md` exists there. After updating the skill, a custom base folder is reset to the default, so name it in your message.
- **I deleted `settings.md`:** the agent asks for your settings again. Your jobs and Applied marks are not affected.
- **Firefox:** not supported, because it cannot access local folders. Use Edge or Chrome.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
