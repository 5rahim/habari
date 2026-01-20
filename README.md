<p align="center">
<img src="./docs/logo-02.png" alt="preview" width="200px"/>
</p>

<h2 align="center"><b>Habari</b></h2>

<h4 align="center">Smart video filename parser written in Go.</h4>

# Use

```go
package main

import (
	habari "github.com/5rahim/habari"
)

func main() {
	data := habari.Parse("Hyouka (2012) S1-2 [BD 1080p HEVC OPUS] [Dual-Audio]")
	println(data.Title)           // Hyouka
	println(data.FormattedTitle)  // Hyouka (2012)
	println(data.Year)            // 2012
	println(data.SeasonNumber)    // []string{"1", "2"}
	println(data.VideoResolution) // 1080p
	
	data = habari.Parse("Jujutsu Kaisen S03E02 One More Time 1080p NF WEB-DL AAC2.0 H 264-VARYG (Jujutsu Kaisen: Shimetsu Kaiyuu - Zenpen, Multi-Subs)")
    println(data.Title) 			// "Jujutsu Kaisen"
    println(data.EpisodeTitle) 		// "One More Time"
    println(data.SeasonNumber) 		// []string{"03"}
    println(data.EpisodeNumber) 	// []string{"02"}
    println(data.VideoResolution)	// "1080p"
    println(data.Source) 			// []string{"WEB-DL"}
    println(data.AudioTerm) 		// []string{"AAC2.0"}
    println(data.VideoTerm) 		// []string{"H 264"}
    println(data.ReleaseGroup) 		// "VARYG"
    println(data.Subtitles) 		// []string{"Multi-Subs"}
}
```

# How it works

Habari uses a **tokenization-based approach** rather than regex patterns, making it more accurate and maintainable than traditional parsers.

1. **Tokenization** - The filename is broken down into tokens (brackets, separators, delimiters, and text chunks)
2. **Classification** - Each token is identified by kind (numbers, words, brackets, CRC32, etc.) and analyzed for context
3. **Keyword Matching** - Tokens are matched against a keyword dictionary (video terms, audio codecs, sources, etc.)
4. **Contextual Parsing** - Multiple parsing strategies identify seasons, episodes, and titles based on token relationships and positions
5. **Smart Merging** - Related tokens are intelligently combined (e.g., "Violet", ".", "Evergarden" → "Violet Evergarden")

**Why this approach is better:**
- No brittle regex patterns that break on edge cases
- Handles complex nested brackets and varied separators naturally
- Context-aware parsing adapts to different filename formats
- Easier to extend with new keywords and patterns
- Better handling of multi-part episodes, season ranges, and alternate numbering


```go
package habari

type Metadata struct {
	Title               string   `json:"title,omitempty"`
	FormattedTitle      string   `json:"formatted_title,omitempty"`
	SeasonNumber        []string `json:"season_number,omitempty"`
	PartNumber          []string `json:"part_number,omitempty"`
	VolumeNumber        []string `json:"volume_number,omitempty"`
	EpisodeNumber       []string `json:"episode_number,omitempty"`
	EpisodeNumberAlt    []string `json:"episode_number_alt,omitempty"`
	OtherEpisodeNumber  []string `json:"other_episode_number,omitempty"`
	AnimeType           []string `json:"anime_type,omitempty"`
	Year                string   `json:"year,omitempty"`
	AudioTerm           []string `json:"audio_term,omitempty"`
	DeviceCompatibility []string `json:"device_compatibility,omitempty"`
	EpisodeTitle        string   `json:"episode_title,omitempty"`
	FileChecksum        string   `json:"file_checksum,omitempty"`
	FileExtension       string   `json:"file_extension,omitempty"`
	FileName            string   `json:"file_name,omitempty"`
	Language            []string `json:"language,omitempty"`
	ReleaseGroup        string   `json:"release_group,omitempty"`
	ReleaseInformation  []string `json:"release_information,omitempty"`
	ReleaseVersion      []string `json:"release_version,omitempty"`
	Source              []string `json:"source,omitempty"`
	Subtitles           []string `json:"subtitles,omitempty"`
	VideoResolution     string   `json:"video_resolution,omitempty"`
	VideoTerm           []string `json:"video_term,omitempty"`
}

```

# Examples



```go
data := habari.Parse("Howl's_Moving_Castle_(2004)_[1080p,BluRay,flac,dts,x264]_-_THORA v2.mkv")
// Title: "Howl's Moving Castle"
// FormattedTitle: "Howl's Moving Castle (2004)"
// Year: "2004"
// AudioTerm: []string{"flac", "dts"}
// FileExtension: "mkv"
// ReleaseGroup: "THORA"
// ReleaseVersion: []string{"2"}
// Source: []string{"BluRay"}
// VideoResolution: "1080p"
// VideoTerm: []string{"x264"}
```


```go
data := habari.Parse("[TV-J] Kidou Senshi Gundam UC Unicorn - episode.02 [BD 1920x1080 h264+AAC(5.1ch JP+EN) +Sub(JP-EN-SP-FR-CH) Chap].mp4")
// Title: "Kidou Senshi Gundam UC Unicorn"
// EpisodeNumber: []string{"02"}
// FileExtension: "mp4"
// ReleaseGroup: "TV-J"
// Source: []string{"BD"}
// AudioTerm: []string{"AAC", "5.1ch"}
// VideoResolution: "1920x1080"
// VideoTerm: []string{"h264"}
// Subtitles: []string{"Sub"}
// Language: []string{"JP", "EN", "FR", "EN", "FR", "CH"}
```

```go
data := habari.Parse("[HorribleSubs] Tsukimonogatari - (01-04) [1080p].mkv")
// Title: "Tsukimonogatari"
// EpisodeNumber: []string{"01", "04"}
// FileExtension: "mkv"
// ReleaseGroup: "HorribleSubs"
// VideoResolution: "1080p"
```
