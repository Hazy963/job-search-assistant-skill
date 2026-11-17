# Job Search Skill

An agent skill that searches job sites for you. You give it a job title and your skills (or a link to your CV), it searches the sites you choose, rates every listing against your profile, and keeps all results in one local list that you browse in a web page.

- Searches any job site you configure, plus Google discovery and optional job-search MCP servers
- Rates each job: Very strong, Strong, Possible or Weak fit, with matches, gaps and caveats
- Never adds the same job twice, so every run only adds new jobs
- A local page with filters, search, sorting, paging, a "Show only new jobs" filter and an "Applied" mark that is saved
- Light and dark theme
- Everything stays on your computer

## Requirements

- An AI agent that supports `SKILL.md` skills and can **use a browser and write files on your computer** (for example Claude Code, Claude Desktop or Cowork, or another agent with a browser tool such as Playwright MCP). A plain web chat cannot write to your disk.
- If you're on the free tier, you can use GitHub Copilot. Its free plan supports Playwright as a plugin.
- Microsoft Edge or Google Chrome to open the results page (it uses the folder-access feature of these browsers).

## Install

1. Download or clone this repository.
2. Install the `job-search` folder (it contains `SKILL.md`, `config.example.json` and `index.html`) as a skill:
   - **Claude Code:** copy `job-search` to `~/.claude/skills/` (all projects) or `.claude/skills/` (one project).
   - **Claude app:** zip the `job-search` folder and add it in the Skills settings.
   - **Other agents:** place the folder where your agent loads skills. If it has no skill support, paste the content of `SKILL.md` as the prompt and give the agent access to the folder.
3. Make sure the agent has a browser tool (for example the Playwright MCP server) and permission to read and write your job-search folder.

## First run

Ask your agent: **"Set up job search"** (or "find jobs for me"). It asks one set of questions:

1. Job title(s) and search terms
2. Your skills, **or a link or file path to your CV**, plus your years of experience
3. Core keywords a job must mention (for example `Python`) and words to exclude
4. Locations and radius
5. Languages
6. Job sites to search
7. Where to store the folder (default `~/job-search`)

If you give a CV, the agent reads it and shows you a short summary to confirm. It keeps only job titles, skills, experience, qualifications and languages. It does **not** store your name, phone, email, address or the CV text.

The agent then creates your folder, `config.json` and the results page, and starts the first search.

## Everyday use

1. Ask: **"Run the job search."**
2. Open `index.html` in your job-search folder with Edge or Chrome.
3. First time only: click **Select job folder** and choose the folder that contains `index.html`. The browser remembers it. If you move the folder, use **Change folder**.
4. After each run, click **Reload data** to see the new jobs.

On the page you can search, filter by fit, level, city and source, sort, and tick **Applied** (applied jobs are hidden by default; use **Only applied** to see them). **Show only new jobs** shows what the latest run found and combines with all other filters.

## Configuration

Edit `config.json` in your job-search folder, or ask the agent to change it. See `job-search/config.example.json`.

| Field | Meaning |
| --- | --- |
| `page_title` | Title shown on the page |
| `profile.job_titles`, `search_terms` | What to search for |
| `profile.core_keywords` | A job is kept only if it names one of these in the title or must-have requirements ("nice to have" does not count) |
| `profile.exclude_keywords` | Jobs mentioning these are skipped |
| `profile.skills`, `experience_years`, `seniority`, `qualifications`, `languages` | Used to rate every job. Skills not listed are never assumed |
| `locations` | List of `{ "city", "radius_km" }` |
| `senior_handling` | `separate` (default, shown with a Senior tag and a stretch check), `exclude` or `include` |
| `sources` | The sites to search (see below) |

### Job sites

Each entry in `sources` is one site:

```json
{ "name": "Indeed", "type": "website", "enabled": true,
  "search_url": "https://de.indeed.com/jobs?q={term}&l={city}&radius={radius}",
  "pages": 3, "extra_pages": 2 }
```

- `type: "website"`: any job site. `search_url` may use `{term}`, `{city}` and `{radius}`. If the pattern stops working, the agent falls back to the site's own search box. Set `enabled` to `false` to switch a site off.
- `type: "google"`: finds listings on other boards and company career pages.
- `type: "mcp"`: an MCP server with a job-search tool (for example [JobsucheMcp](https://github.com/Hazy963/JobsucheMcp) for the German Bundesagentur für Arbeit). Set `tool` and `params`. These are API results without page numbers.

The URL patterns in the example are starting points. Job sites change them, so check them in your browser and adjust.

## Files

```
your-job-search-folder/
  config.json          your settings
  index.html           the results page
  data sources/
    jobs.json          all found jobs, one file
    excluded.json      skipped, not-relevant listings
    applied.json       your Applied marks (written by the page)
```

Duplicates are avoided only from `jobs.json` and `excluded.json`: a job is skipped if its URL or ID is already stored, or if company, title and city match. Existing entries are never changed. You can back up or move the folder like any other.

## Privacy and fair use

- Results, config and Applied marks stay in your folder. Nothing is uploaded by the skill itself. Your agent provider sees what the agent reads, such as job pages and the CV you share.
- Check each job site's terms before automating it. Use the skill for personal job hunting, keep runs modest, and do not try to bypass logins or CAPTCHAs. The skill reports a blocked site instead of working around it.
- Job availability can change. Always check the original listing before applying.

## Troubleshooting

- **The page shows no jobs:** select the folder that contains `index.html` and `data sources`, then click **Reload data**.
- **A site returns nothing:** it may block automation or have changed its URL. Check `search_url`, or disable the site.
- **Page asks for the folder again:** after a browser restart it may ask for permission; click **Select job folder**.
- **"Another run is in progress":** a run was interrupted. Delete `run.lock` if it is older than a few hours.
- **Firefox:** not supported by the page, because it cannot access local folders. Use Edge or Chrome.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
