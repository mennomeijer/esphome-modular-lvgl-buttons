[← Back to main README](../../README.md)

# ui/media_button

Remote-only tile that opens a small quick-actions panel: a Home Assistant scene shortcut, volume up/down buttons for a `media_player` entity, and two optional buttons — an "Off" button (stop + power off a second zone) and a second scene shortcut. Unlike [`media_player`](../media_player/README.md), there is no playback UI or state polling — the buttons on the detail page just fire actions.

## Files

| File | Purpose |
|---|---|
| `remote.yaml` | Tile + wiring — include this in your device config |
| `detail.yaml` | Quick-actions panel — included automatically, do not include directly |

## Variables

| Variable | Required | Description |
|---|---|---|
| `uid` | ✅ | Unique identifier |
| `row` | ✅ | Grid row position (0-based) |
| `column` | ✅ | Grid column position (0-based) |
| `text` | ✅ | Label shown on tile and detail page header |
| `icon` | ✅ | MDI glyph e.g. `$mdi_radio` |
| `scene_entity_id` | ✅ | HA scene entity activated by the scene button, e.g. `"scene.listen_stubru_kitchen"` |
| `scene_text` | ✅ | Label for the scene button |
| `scene_icon` | ✅ | MDI glyph for the scene button |
| `media_player_entity_id` | ✅ | HA media_player entity controlled by the volume buttons |
| `zone2_entity_id` | — | HA media_player entity for an optional "Off" button, e.g. `"media_player.denon_avr_x3100w_2"`. Stops playback (`media_player.media_stop`) then powers the zone off (`media_player.turn_off`). Button is hidden when omitted |
| `zone2_text` | — | Label for the zone 2 button (default: `"Off"`) |
| `zone2_icon` | — | MDI glyph for the zone 2 button (default: `$mdi_speaker_off`) |
| `stop_entity_id` | — | Optional additional HA media_player entity to stop (`media_player.media_stop`) when the zone 2 "Off" button is pressed, e.g. a separate streaming source (Music Assistant, ...) feeding the same zone |
| `scene_2_entity_id` | — | HA scene entity for an optional second scene shortcut, e.g. `"scene.listen_boom_room_kitchen"`. Button is hidden when omitted |
| `scene_2_text` | — | Label for the scene 2 button |
| `scene_2_icon` | — | MDI glyph for the scene 2 button (default: `$mdi_podcast`) |
| `row_span` | — | Number of rows to span (default: `1`) |
| `column_span` | — | Number of columns to span (default: `1`) |
| `page_id` | — | Parent page ID (default: `main_page`) |

## Usage

```yaml
kitchen_radio: !include
  file: esphome-modular-lvgl-buttons/ui/media_button/remote.yaml
  vars:
    uid: kitchen_radio
    row: 1
    column: 1
    text: "Radio"
    icon: $mdi_radio
    scene_entity_id: "scene.listen_stubru_kitchen"
    scene_text: "StuBru"
    scene_icon: $mdi_radio
    media_player_entity_id: "media_player.denon_avr_x3100w_2"
    zone2_entity_id: "media_player.denon_avr_x3100w_2_zone2"
    zone2_text: "Off"
    scene_2_entity_id: "scene.listen_boom_room_kitchen"
    scene_2_text: "Boom Room"
```

## Required glyphs

Add to your device `font:` block:

```
$mdi_chevron_left   $mdi_volume_minus   $mdi_volume_plus
```

Plus whatever glyphs you use for `icon` and `scene_icon` (e.g. `$mdi_radio`), `$mdi_speaker_off` if using the optional zone 2 button (or your own `zone2_icon`), and `$mdi_podcast` if using the optional scene 2 button (or your own `scene_2_icon`).

## Notes

- This is a **remote-only** entity type — there is no `local.yaml`. Scenes and media_players are inherently Home Assistant concepts.
- Short-click on the tile opens the detail page directly — there's no single obvious "primary action" to assign to a tap, so it follows the same convention as the `climate` tile rather than `media_player`'s play/pause toggle.
- The detail page calls `homeassistant.action` directly (`scene.turn_on`, `media_player.volume_up`/`volume_down`, `media_player.media_stop`, `media_player.turn_off`) instead of going through an abstract local/remote script contract, mirroring how the `light` detail page's optional scene shortcuts work.
- The zone 2 "Off" button first stops `stop_entity_id` (if configured), then stops and powers off `zone2_entity_id`.
- Up to four action rows, top to bottom: scene, scene 2, zone 2, volume — laid out on the detail page at fixed vertical positions (y: -100 / -10 / 80 / 170, height 65 each) tuned for a 480px-tall screen. On much shorter screens the bottom row may run close to the edge.
