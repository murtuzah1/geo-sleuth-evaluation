# geo-sleuth Evaluation: Setting Up and Testing an AI Photo-Geolocation Agent

A hands-on log of installing, configuring and testing [geo-sleuth](https://github.com/Oldcircle/geo-sleuth), an open-source agent skill that tries to work out where a photo was taken using OpenStreetMap data, elevation data, satellite imagery and Street View. This repo documents my setup, the problems I solved, and a critical review of one test run. I did not write geo-sleuth; all credit for the tool goes to its author.

## What I did

1. Set up a Windows environment: Node.js, Python, uv, Git and Google Chrome.
2. Installed Gemini CLI as the agent and installed the skill for it.
3. Ran the tool's built-in diagnostics (`doctor.py`, with `--network`) to check every data service it depends on.
4. Ran the pipeline on a photo I took myself and reviewed the output.

## Problems I hit and how I fixed them

| Problem | Cause | Fix |
| --- | --- | --- |
| `npm` blocked: "running scripts is disabled on this system" | PowerShell execution policy | Set the policy to `RemoteSigned` for the current user |
| `node` / `uv` not recognized right after installing | The open terminal had an old PATH | Opened a fresh terminal |
| Skill install failed with `spawn git ENOENT` | Git was not installed | Installed Git for Windows |
| Skill install failed with `EPERM ... C:\Windows\System32` | Ran from a protected folder with project scope | Ran from my home folder with a user-wide install for one agent |
| Claude Code asked for payment | It needs a paid plan or API account | Switched to Gemini CLI |
| Gemini "Sign in with Google" rejected | Google no longer supports that login for individuals in this client | Used API-key authentication instead |
| "Usage limit reached" | Free-tier quota on the default model | Switched to a different model with `/model` |
| Overpass check failed once | Temporary timeout on a public server | Re-ran later; it passed |

## Diagnostics result

All local checks passed. Network probes passed for Overpass (OpenStreetMap), elevation tiles, satellite tiles, Hugging Face, Yandex and Google Street View. Baidu image search failed with a TLS error in one run, which only removes one of the two reverse-image-search engines.

## Test run

- **Input:** one photo I took myself at a public viewpoint (not included here).
- **Agent and model:** Gemini CLI with a free-tier "flash-lite" model.
- **Output:** the agent returned coordinates with a stated error radius and a "high" confidence rating, and used the tool's scripts (metadata and reverse-image intake, OpenStreetMap queries, satellite tiles, Street View lookup).

## Critical review

I do not consider this result proof that the tool is accurate:

- The agent first guessed a place name through a web search, and later steps looked like they were chosen to confirm that guess.
- The map-data step confirmed that a named hill exists at those coordinates. It did not show that the camera was there.
- The satellite and Street View steps confirmed that imagery exists at the chosen point.
- The tool's documented method (matching the skyline against elevation data) was not used, so the stated error radius was not earned.
- This was one photo, run with a small free-tier model. A fair evaluation needs more photos and a stronger model.

## Next steps

- Test 5 to 10 of my own photos at known locations and record the error for each.
- Force the agent to use only the tool's scripts (no web search) and compare results.
- Try a stronger model if quota allows.

## Responsible use

geo-sleuth's own README says to use it on your own photos or ones you have permission to analyze, never to find people who have not agreed to be found. I only tested it on a photo I took myself.

## Credits

geo-sleuth by [Oldcircle](https://github.com/Oldcircle/geo-sleuth) (MIT license). Data sources used by the tool include OpenStreetMap contributors.
