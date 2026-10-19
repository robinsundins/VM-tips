# RB Copenhagen – Instagram-assistent

Hämtar automatiskt obesvarade kommentarer och DM:s, låter Claude skriva svar i RB:s röst
(reglerna i `rb-instagram-community/SKILL.md`) och publicerar **först när du godkänt** varje svar.

```
python rb_instagram.py run      # hämtar allt nytt + skriver drafts.md
python rb_instagram.py review   # j = publicera, r = redigera, h = hoppa över, k = klar
```

## Vad den hämtar

| | Hämtas | Kan publicera |
|---|---|---|
| Kommentarer på dina senaste 12 inlägg (+ trådar) | ✅ obesvarade | ✅ svar, dölja spam |
| DM:s | ✅ där sista meddelandet inte är ditt | ✅ inom 24 h efter deras senaste meddelande |
| Likes | Bara antal per inlägg – Instagram visar inte *vem* som gillat via API | – |
| Story-svar | Kommer in som DM | ✅ |

Gilla, följa eller kommentera på andras konton automatiseras **inte** (bryter mot Instagrams villkor).

## Engångsinstallation (ca 30 min)

1. **Instagram-kontot** måste vara Professional (Creator eller Business): *Inställningar → Kontotyp och verktyg*.
2. **Meta-app:** gå till developers.facebook.com → *My Apps → Create app* → välj *Other / Business*.
   Lägg till produkten **Instagram** → *API setup with Instagram login*.
3. Under *Roles → Instagram testers*: lägg till ditt konto och godkänn i Instagram-appen
   (*Inställningar → Webbplatsbehörigheter → Apparna*).
4. I *Generate access tokens*: koppla kontot och kryssa i
   `instagram_business_basic`, `instagram_business_manage_comments`, `instagram_business_manage_messages`.
   Kopiera token (gäller 60 dagar). Appen behöver ingen granskning så länge den bara används för ditt eget konto.
5. **Claude API-nyckel:** platform.claude.com → *API keys*.
6. På datorn:
   ```
   pip install -r requirements.txt
   cp .env.example .env      # klistra in båda nycklarna
   ```
7. Fyll i `verk.md` (mått, spikar, pris per verk). Det agenten inte hittar där frågar den dig om
   i stället för att gissa, och svar med platshållare som `[mått]` går inte att publicera.

## Köra automatiskt varje morgon

macOS/Linux (`crontab -e`), kl 08:05:
```
5 8 * * * cd /sökväg/till/rb-instagram-agent && /usr/bin/python3 rb_instagram.py run
```
Då ligger `drafts.md` klar när du vaknar. Kör `review` i terminalen när du har fem minuter.

Förläng token var 50:e dag: `python rb_instagram.py refresh-token` och klistra in den nya i `.env`.

## Filer

- `rb_instagram.py` – skriptet
- `rb-instagram-community/` – agentens regler och röst (samma som claude.ai-skillen)
- `verk.md` – fakta agenten får använda
- `data/` – inkorg, utkast och vad som redan hanterats (skapas automatiskt, committas inte)

Kostnad: en körning med 20–30 interaktioner kostar några kronor i Claude API.
