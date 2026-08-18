[← Back to main README](../../README.md)

# ui/media_button

Remote-only tile that opens a small quick-actions panel: one Home Assistant scene shortcut plus volume up/down buttons for a `media_player` entity. Unlike [`media_player`](../media_player/README.md), there is no playback UI or state polling — the three buttons on the detail page just fire actions.

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
```

## Required glyphs

Add to your device `font:` block:

```
$mdi_chevron_left   $mdi_volume_minus   $mdi_volume_plus
```

Plus whatever glyphs you use for `icon` and `scene_icon` (e.g. `$mdi_radio`).

## Notes

- This is a **remote-only** entity type — there is no `local.yaml`. Scenes and media_players are inherently Home Assistant concepts.
- Short-click on the tile opens the detail page directly — there's no single obvious "primary action" to assign to a tap, so it follows the same convention as the `climate` tile rather than `media_player`'s play/pause toggle.
- The detail page calls `homeassistant.action` directly (`scene.turn_on`, `media_player.volume_up`/`volume_down`) instead of going through an abstract local/remote script contract, mirroring how the `light` detail page's optional scene shortcuts work.
