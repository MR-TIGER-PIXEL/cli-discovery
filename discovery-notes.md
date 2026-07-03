## Installation Check

Yes, the installation worked.

I installed the dependencies with `bun install`, checked `package.json`, confirmed that the project had a build script, and ran `bun run build`.

At first, the command did not work because the terminal said:

`zsh: command not found: fubar`

I fixed this by running:

`bun link fubar-cli`

When it still did not work, I added Bun’s bin folder to my PATH with:

`echo 'export PATH="$HOME/.bun/bin:$PATH"' >> ~/.zshrc`

Then I ran:

`source ~/.zshrc`

The command that confirmed `fubar` was available was:

`fubar --version`

It printed:

`1.0.0`

At first glance, `fubar --help` showed that `fubar` is a smart home simulation CLI. It can manage homes, rooms, devices, sensors, automations, occupancy, events, system status, and persisted state. It also supports global options like `--help`, `--version`, `--json`, `--jsonl`, and `--verbose`.


-----

Based on the help output, I think fubar is a command-line tool for managing a simulated smart home. It seems to work with homes, rooms, devices, sensors, automations, occupancy, events, and status. Some commands seem read-only, like status or list commands. Other commands, like create/add or clear, may change data.

-----

The safest command to run first seems to be fubar status because it only shows the current system status. The most important commands seem to be home, room, device, and status because they show the main objects and overall state in the smart home system. Commands like home create, room add, device add, and clear might change data. The evidence is that words like create, add, and clear usually mean making or deleting saved state.

-----

I ran fubar status. It showed the current system status without changing anything. I ran fubar home list. It showed the homes currently saved in the system. These commands helped me inspect the current state before making any changes.

-----

Command 1: fubar status
This command showed the current status of the smart home system. It displayed the saved homes, rooms, devices, sensors, automations, occupancy, and events. Since I had not created anything yet, most of the system was empty. This confirmed my theory that fubar manages a simulated smart home system. Next, I would try a more specific read-only command, like listing homes or rooms.

Command 2: fubar home list
This command showed the homes currently saved in the system. Since I had not created a home yet, the list was empty. This confirmed that fubar stores smart home objects and lets the user inspect them before making changes. Next, I would try one careful state-changing command, such as creating one home, and then verify it with another read-only command.

-----

Before running the command, my prediction was: I think this command will create one new home called My Home in the smart home system. I ran: fubar home create "My Home". Then I verified the result by running: fubar home list. The list showed that My Home had been created, so the command worked.

-----

I ran the normal version: `fubar home list`. The normal output was easier for a human to read because it showed the home in a table. Then I ran the structured version: `fubar home list --json`. The JSON output was easier for software to parse because it used fields like `id`, `name`, and `createdAt`. An AI coding agent might prefer JSON because it can read the exact data without guessing or using more tokens from a visual table.

-----

fubar is a command-line tool for managing a simulated smart home. Its most important commands are home, room, device, status, and clear. It manages objects like homes, rooms, devices, sensors, automations, occupancy, and events. I figured this out by reading the top-level help output, checking command-specific help, running read-only commands, creating one home, and verifying the result. I would tell a classmate to run fubar --help first. A classmate should be careful with fubar clear because it clears saved state.

-----

I ran fubar clear. It cleared the FUBAR database. The command did not ask me for confirmation. It is useful for Codex to start from an empty or reset state because then Codex is not affected by anything I created during my manual discovery. This makes its process easier to observe and compare fairly, because any homes, rooms, or devices that appear afterward were created by Codex.

-----

At first, Codex tried to answer by reading my `discovery-notes.md`, which was not what the lab wanted. Then Codex tried to run `fubar`, but the command was not found because its terminal PATH was not set correctly. After I fixed the PATH in the prompt, Codex ran `fubar --version` and `fubar --help`. Codex used a similar process to mine because it started with the version and help output before making changes. Codex did not discover much that I missed, but it clearly noticed that `fubar clear` is important because it clears persisted state. It also noticed that the database is stored at `~/.fubar/fubar.db` unless `FUBAR_DB_PATH` is set. Codex made an assumption at first when it used my notes instead of only using the CLI. After I clarified the instructions, it avoided reading the repository source code and used the terminal output from `fubar`. By watching Codex use the CLI, I learned that an AI agent needs the correct terminal environment before it can investigate a tool. I also learned that a good agent should use help output, check the version, avoid source code when instructed, and explain what it learned from command output.

-----

Codex identified several creatable objects in fubar: homes, rooms, devices, sensors, and automation rules.

Codex said the commands would include fubar home create <name>, fubar room add <name>, fubar device add <room> <type> <name>, fubar sensor add <room> <type> <name>, and fubar automation create [name].

Codex also explained that occupancy can be changed with fubar occupancy set, but that this updates a room’s occupancy instead of creating a separate object. It said events seem to be inspectable, not directly created.

For creating three rooms in a house, Codex said it would use fubar room add three times. If the house did not exist yet, it would first run fubar home create "My House".

Codex explained its reasoning from fubar help output and command-specific help. Its explanation matched what I discovered manually because both of us found that fubar manages a simulated smart home through homes, rooms, devices, sensors, automations, and status.

-----

Codex used `fubar` commands to inspect the smart home state after creating the rooms. It reported that `My Home` has 3 rooms: `bathroom`, `bedroom`, and `kitchen`. The result matched what Codex created earlier. Verification is important when an AI agent changes something because it confirms that the command actually worked and that the final state matches the request.

-----

My manual discovery process was similar to Codex’s process because we both used help output, checked commands, and started with safe read-only commands before changing anything.

Codex moved from discovering the tool to using it by translating a natural-language request into specific CLI commands. For example, it turned the request to create three rooms into fubar home create and several fubar room add commands.

This lab showed me that both people and AI agents can understand an unfamiliar CLI by reading help output, forming hypotheses, running small experiments, using structured output like JSON, and verifying results after making changes.

-----

## Stretch Lab: Teach fubar a New Trick

### What CRUD Means

CRUD means Create, Read, Update, and Delete. These are four basic actions that software systems often support for important objects.

* Create means making a new object.
* Read means viewing or inspecting existing objects.
* Update means changing an existing object.
* Delete means removing an object.

### What Missing Capability I Chose

I chose to add a delete command for sensors.

From the CRUD table, sensors could already be created, listed, checked, and triggered, but there was no direct command to remove a sensor. That meant sensors were missing a clear Delete operation.

The new command I chose was:

```bash
fubar sensor remove <sensor>
```

### What Files Codex Changed

Codex changed these files:

```text
src/commands/sensor.ts
src/services/smart-home-service.ts
src/storage/repository.ts
tests/smoke.test.ts
```

Codex added the `remove <sensor>` command to the sensor command file. It added service logic to remove a sensor. It added repository logic to delete a sensor by id or name. It also added a smoke test to prove that a removed sensor disappears from the sensor list.

### How I Tested the New Command

I tested the new command from the terminal.

First, I checked that the new command existed:

```bash
fubar sensor --help
```

Then I created a small example:

```bash
fubar clear
fubar home create "Demo Home"
fubar room add kitchen --home "Demo Home"
fubar sensor add kitchen motion kitchen-motion
fubar sensor list --room kitchen --json
```

The list command showed that the `kitchen-motion` sensor existed.

Then I removed the sensor:

```bash
fubar sensor remove kitchen-motion
```

After that, I listed the sensors again:

```bash
fubar sensor list --room kitchen --json
```

### What Command Proved the Behavior Works

The command that proved the new feature worked was:

```bash
fubar sensor list --room kitchen --json
```

After removing `kitchen-motion`, the final JSON output showed:

```json
{
  "ok": true,
  "data": []
}
```

This proved that the sensor was removed successfully.

### Why This Change Matters

This change matters because it makes fubar’s sensor commands more complete. Before this stretch, sensors could be created and inspected, but they could not be directly removed. Adding `fubar sensor remove <sensor>` gives sensors a clearer CRUD workflow because they now have a direct Delete command.
