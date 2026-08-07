# AGENTS.md

## Project overview

This repository contains a single-page browser auto-battle game.

- Primary file: `index.html`
- Deployment target: GitHub Pages
- Main branch: `main`
- The project is intentionally kept as a single HTML file unless the user explicitly requests a structural refactor.
- Character logic, simulation logic, UI, rendering, effects, balance values, patch notes, and version text are all maintained inside `index.html`.

## Required workflow

Before changing anything:

1. Read the latest `main` branch version of `index.html`.
2. Identify the current displayed version and newest patch note.
3. Inspect the exact implementation related to the request instead of relying only on the user's description.
4. Preserve all unrelated characters and systems.

After changing anything:

1. Increase the version by exactly 1 unless the user specifies another version.
2. Update both the document title and visible main title.
3. Add one concise patch-note entry at the top of the patch-note list.
4. Keep the output filename as `index.html`.
5. Run a JavaScript syntax check, preferably by extracting the main script and running `node --check`.
6. For every balance, character, or combat-rule patch, automatically run the relevant in-browser battle simulation before reporting completion. Use the selected-character 30-battle-per-opponent test for a character balance patch when available, and report win/loss/draw plus average remaining HP.
7. Run focused checks for the changed logic when practical.
8. Commit the completed change to `main` unless the user asks for a branch or pull request.
9. Report the version, concise change summary, test result, and commit SHA.

## Editing rules

- Do not delete, rename, simplify, or rebalance unrelated characters.
- Do not change balance values that were not requested.
- Do not rewrite large sections merely for style when a focused patch is sufficient.
- Preserve existing character IDs unless the character itself is explicitly deleted or replaced.
- Keep in-game descriptions compact. The user does not want long explanations inside character cards or tooltips.
- Preserve existing visual identity and character concept colors unless the user requests a redesign.
- HTML/CSS/Canvas effects must remain reproducible directly in the browser. Do not add external generated-image dependencies unless explicitly requested.
- Avoid introducing build tools, frameworks, package managers, or multiple source files without explicit approval.

## Simulation and performance rules

- Automated simulation must minimize rendering, sound, particles, logs, and DOM updates while calculations are running.
- Long simulations must yield control back to the browser periodically so the page does not appear frozen.
- Prefer staged tests over automatically running 100 battles against every character.
- Current intended test structure:
  - Fast all-matchup test: 10 battles per opponent.
  - Extreme 10-battle results may receive 20 additional battles.
  - Selected-character precision test: 30 battles per opponent.
  - Dedicated matchup test: up to 100 battles for one selected opponent.
- Dummy characters must not be included in normal full-participation or matchup-stat tests unless explicitly requested.
- During simulation optimization, preserve combat results as closely as possible. Do not silently replace the real combat engine with an approximate statistical model.

## Balance-analysis conventions

- Treat very small samples as screening data, not final balance proof.
- Ten battles are suitable for finding extreme matchups, not for precise win-rate conclusions.
- Prefer fixing mechanical bugs before adjusting numerical balance.
- When changing a new character, avoid changing older characters merely to compensate unless explicitly requested.
- Preserve intended hard counters if the user says they are acceptable.

## Current character-design principles

- Roles include warrior, mage, guardian, assassin, support, and marksman.
- Each character should have a clear, distinct combat identity.
- New guardian characters should control space, absorb pressure, protect themselves, or punish attacks without simply copying an existing guardian.
- Effects, status text, gauges, and combat-analysis output should reflect the character's actual mechanics.

## Current high-priority context: Prism Guardian Iris

Character:

- ID: `prism_iris`
- Name: `채광수호 이리스`
- Role: guardian
- Core mechanic: the arena is divided into a fixed number of randomly shaped stained-glass regions. When Iris takes damage, an unlit region becomes lit. Lit regions damage enemies standing on them. When all regions are lit, Iris triggers a completion effect, gains temporary damage reduction, resets the field, and generates a new random pattern.

Important intended behavior:

- Lit stained-glass damage is a slow periodic area effect.
- Standing across multiple lit glass regions must not multiply the periodic damage.
- The cooldown must be tracked per enemy, globally across all lit Iris regions.
- An enemy standing on one or more lit regions should take exactly one periodic hit per configured interval.
- Briefly leaving a region or crossing a region boundary must not reset the remaining periodic-damage cooldown and cause rapid repeated hits.
- Iris receives a low permanent damage-reduction benefit while standing on any lit region.
- Completing all regions grants a stronger temporary damage-reduction buff, currently intended to last 10 seconds.
- The completion reward is damage reduction, not a shield.

## Next known task

The current stained-glass damage implementation may still track cooldowns per glass cell, allowing overlapping or boundary-touching cells to apply several `-2` hits in rapid succession. Fix this by using a single per-enemy cooldown owned by Iris or the Iris field, not by each individual cell.

Target behavior for the next patch:

- Version: increment from the current repository version.
- If an enemy is touching at least one lit stained-glass region, apply exactly 2 damage once per 3.5 seconds.
- Multiple simultaneous lit-region contacts still produce only one hit.
- Leaving and quickly re-entering must not reset the cooldown.
- Keep unrelated Iris values unchanged unless the user requests additional balance changes.

## Communication style

- Use Korean when reporting to the user.
- Keep completion reports compact.
- Mention only implemented changes, tests actually run, and any unresolved uncertainty.
- Never claim a functional test passed unless it was actually executed.
