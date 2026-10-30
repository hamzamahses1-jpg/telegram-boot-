import json
import os
import time
import urllib.request
import urllib.error

BOT_TOKEN = os.environ["8593897281:AAHIAGYB1ZJf3Uf9nmZ8Qc6SqYaKEHQwI-s"]
SOURCE = -1004332120884   # القناة 1 (ABED)
TARGET = -1004438136058   # القناة 2 (Traiding)
RUN_SECONDS = 5 * 3600 + 45 * 60

API = f"https://api.telegram.org/bot{BOT_TOKEN}"


def call(method, **params):
    req = urllib.request.Request(
        f"{API}/{method}",
        data=json.dumps(params).encode("utf-8"),
        headers={"Content-Type": "application/json"},
    )
    try:
        with urllib.request.urlopen(req, timeout=60) as r:
            return json.loads(r.read().decode("utf-8"))
    except urllib.error.HTTPError as e:
        return json.loads(e.read().decode("utf-8"))


def main():
    me = call("getMe")
    print("Bot:", me.get("result", {}).get("username"), flush=True)
    call("deleteWebhook")
    offset = None
    end = time.time() + RUN_SECONDS
    print("Bot is running...", flush=True)
    while time.time() < end:
        try:
            res = call(
                "getUpdates",
                offset=offset,
                timeout=30,
                allowed_updates=["channel_post"],
            )
            if not res.get("ok"):
                print("getUpdates error:", res, flush=True)
                time.sleep(5)
                continue
            for upd in res["result"]:
                offset = upd["update_id"] + 1
                post = upd.get("channel_post")
                if not post or post["chat"]["id"] != SOURCE:
                    continue
                out = call(
                    "copyMessage",
                    chat_id=TARGET,
                    from_chat_id=SOURCE,
                    message_id=post["message_id"],
                )
                if not out.get("ok"):
                    print("Copy error:", out, flush=True)
                else:
                    print("Copied", post["message_id"], flush=True)
        except Exception as e:
            print("Error:", e, flush=True)
            time.sleep(5)
    if offset is not None:
        call("getUpdates", offset=offset, timeout=0)
    print("Done, next run continues.", flush=True)


main()
