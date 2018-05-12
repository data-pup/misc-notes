# !!Con Notes

This is a loose notes document containing various items of interest from
!!con 2018 talks.

### Keynote - Mimi Onuoha

github: @MimiOnuoha

"Embedded in every technology there is a powerful idea" -Neil Postman

`pulse (2015)`
`pathways (2014)`

"Notes on Algorithmic Violence"

### Storytelling with `traceroute` - `@tetrakazi`

`traceroute  shows the path that a request takes to arrive at a location.

Shows hostnames, IP addresses, and 3 latency times.

example: `traceroute -I github.com`

TTL, number that is decremented at each step. If this reaches 0, expired
message is sent to the original sender.

### Undefined Characters - `@pzula`

cmap Table, character to glyph index mapping table.

Codepoints can be represented as hexadecimal values, a -> U0061

`TTFunk` is an example of a library to help with this :)

Specification for a font requires that 0 is reserved for `notdef`.

Codepoints are used to find the glyph ID. If the codepoint is not found, then
the glyph id 0 is returned.

### Turning Google Earth into SimCity 2000 - `@obtusatum`

Steina Vasulka, "The West", "Warp"

Jenny Odell "Satellite Collections"

Tom Sherman "Appearance, Memory, and Influence"

### We Built A Map To Aggregate Real Time Flood Data In Under Two Days - `@arunasank`

github.com/osm-in/flood-map

---

### Compressing the Library of Babel

Kolmogorov Complexity, pigeonhole principle

### Solving Pokemon Blue with a Regular Expression - `@hausdorff_space`

all FSM's can be represented with a regex ?? (look into this)

### Transforming Video Streams with Live Code and a REPL - `@markwunsch`

Live coding resources:

extempore, sonic pi, overtone

overtone.github.io
toplap.org
livecode.nyc

