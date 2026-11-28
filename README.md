# ShowingTime API integration and MCP server: connect ShowingTime to ChatGPT, Claude and any AI agent

**ShowingTime API integration and MCP server by Cotlet.dev: yes, Claude, ChatGPT and other AI agents can work in your ShowingTime account.** ShowingTime has no public API, no official MCP server and no ChatGPT or Claude app for agents (checked 26 Nov 2026). Cotlet.dev connects your own signed-in ShowingTime account to any AI agent, so you ask in plain words and the AI helps you manage showings and feedback.

## What you can ask

> Summarize this week's showings and feedback for 22 Birch Ln and draft the update for my seller.

> Which showings last week have no feedback yet? Draft a short request to each buyer agent.

> List showing requests waiting for my confirmation and confirm the ones that fit my seller's rules after my OK.

> Which of my showing requests are still unconfirmed after 4 hours? Give me the listing agent's phone and draft a text.

> Request showings for these 5 listings on Saturday between 10 and 2, in route order.

## What listing and buyer agents ask their AI to do in ShowingTime

From listing and buyer agents' posts on Reddit, forums, review sites and the ShowingTime help center (checked 26 Nov 2026).

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Weekly seller report with feedback | "Summarize this week's showings and feedback for 22 Birch Ln and draft the update for my seller." | Weekly |
| Chase missing feedback | "Which showings last week have no feedback yet? Draft a short request to each buyer agent." | Daily |
| Answer showing requests fast | "List showing requests waiting for my confirmation and confirm the ones that fit my seller's rules after my OK." | Hourly |
| Chase my unconfirmed requests | "Which of my showing requests are still unconfirmed after 4 hours? Give me the listing agent's phone and draft a text." | Hourly |
| Book a tour of several homes | "Request showings for these 5 listings on Saturday between 10 and 2, in route order." | When you ask |
| One daily digest instead of many texts | "Every evening give me one list of tomorrow's showings on my listings and my buyer tours." | Daily |
| Update showing instructions | "Update the showing instructions on my listing: new lockbox code from Friday, attach the disclosure." | When you ask |
| Showing numbers for a price talk | "How many showings and second showings did each of my listings get in the last 30 days?" | Your own endpoint, called from a sheet or n8n |

Your AI asks you before it sends, books, replies or changes anything. Payments stay in your hands.

## ShowingTime API

ShowingTime has no public API for agents or offices; Bridge Interactive distributes MLS data to MLSs, and the calendar only exports as iCal (checked 26 Nov 2026). With Cotlet.dev you build your own ShowingTime API: show your AI agent a job once in your signed-in ShowingTime account, save it as an endpoint and call it from n8n, Make, Zapier or your own code. How it works: https://cotlet.dev/integrations/api/showingtime

Endpoints you can build in a few minutes (none of them exists until you build it):

- `missing-feedback`: Showings without feedback after N days
- `upcoming-showings`: Showings on your listings and buyer tours for a date
- `showing-feedback`: Feedback per showing for a listing
- `pending-requests`: Requests waiting for your confirmation
- `unconfirmed-requests`: Your buyer requests not confirmed yet
- `listing-activity`: Showings, second showings and feedback counts per listing
- `request-showing`: A showing request for a listing and time, after your OK

Call one with your Cotlet API key: `POST https://cotlet.dev/api/v1/custom/missing-feedback`. One call runs up to 90 seconds, writes wait for your OK, payments are blocked.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its ShowingTime tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"showingtime":{"command":"uvx","args":["showingtime-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t showingtime-mcp .
docker run -i --rm -e COTLET_API_KEY showingtime-mcp
```

Tools: `showingtime_guide`, `showingtime_status`, `showingtime_open`, `showingtime_read`, `showingtime_click`, `showingtime_type`, `showingtime_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install showingtime-mcp        # or run it once: uvx showingtime-mcp connect
showingtime-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 showingtime_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 showingtime_agent.py status    # is ShowingTime ready in your Cotlet.dev account
python3 showingtime_agent.py guide     # ShowingTime screens and sign-in link
python3 showingtime_agent.py read      # open ShowingTime, read the page, close the browser
python3 showingtime_agent.py tools     # list Cotlet.dev tools
python3 showingtime_agent.py serve     # start the stdio MCP server (same as no argument)
```

## What you can ask your AI to do in ShowingTime

These are real jobs ShowingTime users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in ShowingTime account.

### Seller updates and feedback

#### Can I send ShowingTime feedback to my seller every week without copying it by hand?

Partly. ShowingTime has a Listing Activity Report with showings and feedback per listing, and a way to share feedback with your seller. With Cotlet, your agent reads the week's showings and feedback for a listing and drafts the update for your seller. Nothing is sent until you say yes.

Ask your agent: "Summarize this week's showings and feedback for 22 Birch Ln in ShowingTime and draft the update for my seller."

As an API endpoint: Yes. Save the weekly read as an endpoint in Cotlet that returns showings and feedback per listing, then call it every Friday from n8n, Make or Zapier to email each seller after your OK.

#### Which of my ShowingTime showings last week have no feedback yet, and can AI ask each buyer agent for it?

Yes. With Cotlet, ChatGPT or Claude reads last week's showings on your listings in ShowingTime, lists the ones with no feedback and drafts a short request to each buyer agent. Nothing is sent until you say yes.

Ask your agent: "List last week's ShowingTime showings on my listings with no feedback and draft a short request to each buyer agent."

As an API endpoint: Yes. Save the missing-feedback check as an endpoint in Cotlet and call it every morning from n8n, Make or Zapier to get the list in Slack or email.

### Requests and confirmations

#### Can AI list the showing requests waiting for my confirmation in ShowingTime and confirm the ones that fit my seller's rules?

Partly. ShowingTime can confirm some appointments on its own when the listing's settings allow it. With Cotlet, your agent lists the requests still waiting, checks each against the rules you give and confirms the fitting ones. Nothing changes until you say yes.

Ask your agent: "List the ShowingTime requests waiting for my confirmation and mark which fit my seller's rules. Confirm them only after my OK."

As an API endpoint: Yes. Save the pending-request list as an endpoint in Cotlet and call it every hour from n8n, Make or Zapier to see new requests early.

#### Which of my ShowingTime requests are still unconfirmed after 4 hours, and who do I call?

Partly. ShowingTime shows the status of each request you made. With Cotlet, your agent lists the ones still unconfirmed after 4 hours, reads the listing agent's phone from the showing details and drafts a text for you to send. Cotlet does not send texts itself.

Ask your agent: "List my ShowingTime showing requests unconfirmed after 4 hours with the listing agent's phone and draft a short text for each."

As an API endpoint: Yes. Save the unconfirmed-request list as an endpoint in Cotlet and call it every hour from n8n, Make or Zapier to get a ping when a request is stuck.

#### Can AI request showings for five listings on Saturday in route order in ShowingTime?

Yes. With Cotlet, your agent opens each listing in ShowingTime, requests a time inside your Saturday window and orders the tour by route. Nothing is requested until you say yes.

Ask your agent: "Request ShowingTime showings for these 5 listings on Saturday between 10 and 2, in route order. Show me the plan before you request anything."

As an API endpoint: Yes. Save the request step as an endpoint in Cotlet that takes a listing and a time, and call it from n8n or Make after your OK.

### Daily list and instructions

#### Can I turn ShowingTime texts into one summary of tomorrow's showings every evening?

Partly. ShowingTime keeps sending its own texts and emails, and you set those yourself. With Cotlet, your agent reads tomorrow's showings on your listings and your buyer tours each evening and gives you one list, so you stop reading each text.

Ask your agent: "Every evening give me one list of tomorrow's ShowingTime showings on my listings and my buyer tours, with time, address and agent."

As an API endpoint: Yes. Save the daily list as an endpoint in Cotlet and call it every evening from n8n, Make or Zapier to get it by email or Slack.

#### Can AI update the showing instructions on my ShowingTime listing with a new lockbox code and attach the disclosure?

Yes. With Cotlet, your agent opens the listing's showing instructions in ShowingTime, adds the new code with the date it starts and attaches the disclosure. Nothing changes until you say yes.

Ask your agent: "Update the showing instructions on 22 Birch Ln in ShowingTime: new lockbox code from Friday, attach the disclosure. Show me the change first."

As an API endpoint: Yes. Save the change as an endpoint in Cotlet that takes a listing and the new text, and call it from n8n or Make after your OK.

### Listing numbers

#### How many showings and second showings did each of my ShowingTime listings get in the last 30 days?

Yes. ShowingTime's Listing Activity Report shows showings and feedback per listing. With Cotlet, your agent runs it for each of your listings over 30 days and puts showings, repeat showings and feedback side by side for your price talk.

Ask your agent: "For each of my ShowingTime listings, show showings, second showings and feedback received in the last 30 days in one table."

As an API endpoint: Yes. Save the listing-activity read as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier to fill a Google Sheet.

## Questions from ShowingTime users

**Does ShowingTime have an API?**

ShowingTime has no public API for agents or offices; Bridge Interactive distributes MLS data to MLSs, and the calendar only exports as iCal (checked 26 Nov 2026). With Cotlet.dev you build your own ShowingTime API on your signed-in account and call it from n8n, Make or your own code: https://cotlet.dev/integrations/api/showingtime

**How do I connect ShowingTime to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT (Settings → Apps, connector URL `https://cotlet.dev/mcp/v1`), sign in to ShowingTime once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No code and no ShowingTime API key.

**Is there a way to use Claude with ShowingTime?**

Yes. In Claude Code run the one-line `claude mcp add` command above; in the Claude app add `https://cotlet.dev/mcp/v1` as a custom connector. Claude then works in your own ShowingTime account.

**Is there a ShowingTime MCP server?**

ShowingTime has no official MCP server for your account. Cotlet.dev is a hosted MCP server that works in your own ShowingTime account for you, with Claude, ChatGPT, Cursor, Codex, n8n, Make and Zapier.

**Can I send ShowingTime feedback to my seller every week without copying it by hand?**

Yes. Your AI reads the week's showings and feedback for a listing in your signed-in ShowingTime account and drafts the seller update. You send it after a look.

**Does ShowingTime have an API?**

No public one (checked 26 Nov 2026). Bridge Interactive serves MLS data feeds, not agent accounts, and the calendar exports only as iCal. Cotlet.dev works in your own ShowingTime account, and you can build your own endpoints on it.

## Find it on

- [ShowingTime on Cotlet.dev](https://cotlet.dev/integrations/mcp/showingtime?utm_source=github&utm_medium=readme&utm_campaign=showingtime&utm_content=find)
- [PyPI package showingtime-mcp](https://pypi.org/project/showingtime-mcp/)
- [Odel](https://odel.app/modules/cotlet/showingtime)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/showingtime/)

## Also works with

- [Mangomint MCP](https://github.com/cotlet-connect/mangomint-mcp)
- [Wanderlog MCP](https://github.com/cotlet-connect/wanderlog-mcp)
- [SmugMug MCP](https://github.com/cotlet-connect/smugmug-mcp)
- [Garmin Connect MCP](https://github.com/cotlet-connect/garmin-mcp)
- [Housecall Pro MCP](https://github.com/cotlet-connect/housecall-pro-mcp)
- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Crexi MCP](https://github.com/cotlet-connect/crexi-mcp)
- [DistroKid MCP](https://github.com/cotlet-connect/distrokid-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
- [EstimateOne MCP](https://github.com/cotlet-connect/estimateone-mcp)
- [Google Search Console MCP](https://github.com/cotlet-connect/google-search-console-mcp)
- [Lichess MCP](https://github.com/cotlet-connect/lichess-mcp)
- [Strava MCP](https://github.com/cotlet-connect/strava-mcp)
- [StreamElements MCP](https://github.com/cotlet-connect/streamelements-mcp)
- [Meetup MCP](https://github.com/cotlet-connect/meetup-mcp)
- [AccuRanker MCP](https://github.com/cotlet-connect/accuranker-mcp)
- [GetYourGuide MCP](https://github.com/cotlet-connect/getyourguide-mcp)
- [Idealista MCP](https://github.com/cotlet-connect/idealista-mcp)
- [Riverside MCP](https://github.com/cotlet-connect/riverside-mcp)
- [Udemy MCP](https://github.com/cotlet-connect/udemy-mcp)
- [Faire MCP](https://github.com/cotlet-connect/faire-mcp)
- [Teachable MCP](https://github.com/cotlet-connect/teachable-mcp)
- [Vagaro MCP](https://github.com/cotlet-connect/vagaro-mcp)
- [Buildertrend MCP](https://github.com/cotlet-connect/buildertrend-mcp)
- [Zoopla MCP](https://github.com/cotlet-connect/zoopla-mcp)
- [Dribbble MCP](https://github.com/cotlet-connect/dribbble-mcp)
- [Hostelworld MCP](https://github.com/cotlet-connect/hostelworld-mcp)
- [Mashvisor MCP](https://github.com/cotlet-connect/mashvisor-mcp)
- [Rent Manager MCP](https://github.com/cotlet-connect/rent-manager-mcp)
- [ThriveCart MCP](https://github.com/cotlet-connect/thrivecart-mcp)
- [Fresha MCP](https://github.com/cotlet-connect/fresha-mcp)
- [Booksy MCP](https://github.com/cotlet-connect/booksy-mcp)
- [BookingKoala MCP](https://github.com/cotlet-connect/bookingkoala-mcp)
- [Milanote MCP](https://github.com/cotlet-connect/milanote-mcp)
- [Partiful MCP](https://github.com/cotlet-connect/partiful-mcp)
- [Contractor Foreman MCP](https://github.com/cotlet-connect/contractor-foreman-mcp)
- [Pixieset MCP](https://github.com/cotlet-connect/pixieset-mcp)
- [GlossGenius MCP](https://github.com/cotlet-connect/glossgenius-mcp)
- [ZenMaid MCP](https://github.com/cotlet-connect/zenmaid-mcp)
- [AroFlo MCP](https://github.com/cotlet-connect/aroflo-mcp)
- [Simpro MCP](https://github.com/cotlet-connect/simpro-mcp)
- [Workiz MCP](https://github.com/cotlet-connect/workiz-mcp)
- [Kickserv MCP](https://github.com/cotlet-connect/kickserv-mcp)
- [Service Autopilot MCP](https://github.com/cotlet-connect/service-autopilot-mcp)
- [Mindbody MCP](https://github.com/cotlet-connect/mindbody-mcp)
- [DaySmart MCP](https://github.com/cotlet-connect/daysmart-mcp)
- [Acuity Scheduling MCP](https://github.com/cotlet-connect/acuity-scheduling-mcp)
- [Jane App MCP](https://github.com/cotlet-connect/jane-app-mcp)
- [FieldEdge MCP](https://github.com/cotlet-connect/fieldedge-mcp)
- [Phorest MCP](https://github.com/cotlet-connect/phorest-mcp)
- [DINGG MCP](https://github.com/cotlet-connect/dingg-mcp)
- [Invoice Simple MCP](https://github.com/cotlet-connect/invoice-simple-mcp)
- [PhotoShelter MCP](https://github.com/cotlet-connect/photoshelter-mcp)
- [RazorSync MCP](https://github.com/cotlet-connect/razorsync-mcp)
- [ShootProof MCP](https://github.com/cotlet-connect/shootproof-mcp)
- [Invoice2go MCP](https://github.com/cotlet-connect/invoice2go-mcp)
- [Appointy MCP](https://github.com/cotlet-connect/appointy-mcp)
- [Contractor+ MCP](https://github.com/cotlet-connect/contractor-plus-mcp)
- [Petpooja MCP](https://github.com/cotlet-connect/petpooja-mcp)
- [Teachers Pay Teachers MCP](https://github.com/cotlet-connect/teachers-pay-teachers-mcp)
- [Bloom MCP](https://github.com/cotlet-connect/bloom-mcp)
- [hipages MCP](https://github.com/cotlet-connect/hipages-mcp)
- [ServiceSeeking MCP](https://github.com/cotlet-connect/serviceseeking-mcp)
- [MyJobQuote MCP](https://github.com/cotlet-connect/myjobquote-mcp)
- [MyBuilder MCP](https://github.com/cotlet-connect/mybuilder-mcp)
- [Yelp for Business MCP](https://github.com/cotlet-connect/yelp-for-business-mcp)
- [Rosie MCP](https://github.com/cotlet-connect/rosie-mcp)
- [Roofr MCP](https://github.com/cotlet-connect/roofr-mcp)
- [FieldPulse MCP](https://github.com/cotlet-connect/fieldpulse-mcp)
- [YourTradebase MCP](https://github.com/cotlet-connect/yourtradebase-mcp)
- [JobNimbus MCP](https://github.com/cotlet-connect/jobnimbus-mcp)
- [Ascora MCP](https://github.com/cotlet-connect/ascora-mcp)
- [eLocal MCP](https://github.com/cotlet-connect/elocal-mcp)
- [Alto MCP](https://github.com/cotlet-connect/alto-mcp)
- [Rightmove Plus MCP](https://github.com/cotlet-connect/rightmove-plus-mcp)
- [TurboTenant MCP](https://github.com/cotlet-connect/turbotenant-mcp)
- [Innago MCP](https://github.com/cotlet-connect/innago-mcp)
- [Property Finder MCP](https://github.com/cotlet-connect/property-finder-mcp)
- [LoopNet MCP](https://github.com/cotlet-connect/loopnet-mcp)
- [Dotloop MCP](https://github.com/cotlet-connect/dotloop-mcp)
- [SkySlope MCP](https://github.com/cotlet-connect/skyslope-mcp)
- [connectMLS MCP](https://github.com/cotlet-connect/connectmls-mcp)
- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/mcp/showingtime?utm_source=github&utm_medium=readme&utm_campaign=showingtime

Start free: https://cotlet.dev/signup?app=showingtime&utm_source=github&utm_medium=readme&utm_campaign=showingtime
