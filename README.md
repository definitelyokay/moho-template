# Moho Project Template

A template for saving Moho files in a git repo. Moho files are tracked as binary files.

[Forum post](https://www.lostmarble.com/forum/viewtopic.php?t=33865) for this repository.

Moho files aren't merge-compatible. If multiple people need to work on the same file, they need to take turns and coordinate on their own.

## Project Setup

Moho files should be placed in `src/`, organized how you see fit.

You have two options for file types as of Moho 13:

1. Disable thumbnails (see note below) and just save `*.moho` files in `src`. The `*.moho` files are essentially zip files with a `json` file (ending in `*.mohoproj`), a thumbnail, and any embedded resources.
2. Save the file, but rename it to `*.zip` and extract it, taking the json `*.mohoproj` file and any resources and putting them in a folder. Moho 13 can open and edit `*.mohoproj` files just fine by themselves.

It doesn't matter either way because both `*.moho` and `*.mohoproj` files are tracked using [git-lfs](https://git-lfs.github.com/), so make sure you have that setup.

**IMPORTANT**: Moho 13 has a setting to disable generation of thumbnails when saving files. Make sure its turned on via `Edit->Preferences->Document->Document preview thumbnail size->No Thumbnails`.

**NOTE:** For more information about Moho and Anime Studio's file formats, see the [official Anime Studio file format document](https://web.archive.org/web/20190516163203/https://my.smithmicro.com/downloads/AnimeStudioDocumentFileFormat.pdf).

## I'm Stuck! Help?

Here are some useful links to aid you in your Moho quest:

- [Lost Marble Moho Forum](https://www.lostmarble.com/forum/) — still active in 2020, this is the best place to ask questions and get help with advanced topics.

### Tips

Here is a compilation of tips based on my own misconceptions and adventures:

- **Question**: How do I keep bones from manipulating/distorting unrelated vector layers?
	**Simple Answer**: Create your shapes far apart, and then use bone translation to tie your model together. If that's not satisfactory...
	**Advanced Answer**: [Bone layers can be nested!](http://www.lostmarble.com/forum/viewtopic.php?t=25172) Vector layers *can only be manipulated by bones in the nearest ancestor bone layer*.
	Additionally, you may create smart bones in a parent bone layer that control bones in the child bone layer(s) to avoid having to select the child bone layer.
- **Question**: How do I use pin bones to make a 2D bone dial to control spherical movement (eyes, head turns, etc)?
	**Answer** [How to make Smart Bones for Eyes](https://www.youtube.com/watch?v=en7UDGDwSCM)
	The advantage in this method is that the second set of bones (with the circle and the targets) doesn't necessarily have to be in the same layer as the first set.  So the basic control bones can be in the 'head' bone layer and the second set with the target bones can be in the 'body' layer with the rest of the visible limb bones, which keeps the rig streamlined.  You don't have to go into nested layers to move the fine parts of the face.
	If you just need simple pin bone tracking, [look at this simpler version](https://www.youtube.com/watch?v=xFvBELCuKyc&feature=youtu.be)
	Another example: [Robin hood Head turning](https://www.youtube.com/watch?v=SAxRZXxiRpI)


## Moho Production Pipeline

Moho has been used professionally, although it is not the most popular choice.

The Irish animation studio *Cartoon Saloon* successfully used Moho to produce the tv show *Puffin Rock*.

- [Making Puffin Rock - Moho in a TV Series Pipeline: Webinar Replay](https://www.youtube.com/watch?v=EaA3M4DO6ZQ)
- [Jeremy from Cartoon Saloon explains his Anime Studio Pro Workflow](https://www.youtube.com/watch?v=6diWZnQu3HM)

The description of the webinar video describes the following goals that were achieved:

> - Bringing a character from design to compositing
> - Seeing inside a character model
> - Working with a studio in another country on the same files
> - Seeing what a scene file is made up of

### Software Pipeline

Cartoon Saloon successfully used the following software with Moho to manage the project:

- [Shotgun](https://www.shotgunsoftware.com/) is a database used to store shots and assets.
- [Deadline](https://www.awsthinkbox.com/deadline) is a render farm that integrates with Shotgun [and supports Moho](https://docs.thinkboxsoftware.com/products/deadline/10.1/1_User%20Manual/manual/app-anime-studio.html#app-anime-studio-ref-label).

If running something like Deadline on AWS is overkill, [you can use Moho from the command-line](http://www.lostmarble.com/forum/viewtopic.php?t=1318).

### Layer References

Moho's layer references enable collaboration.

- Master model file can contain the drawings and rig.
- Child model file is created that references the master model file.
- Scene file imports the child model to allow the master model file to be edited by artists/riggers.

By abstracting between the master and child model, those working in a scene were able to import the last working version of the model, since updating layer references to match with the original is a manual process.

```
┌─────────┐   ┌───────┐   ┌─────────┐
│ Master  │   │ Child │   │  Scene  │
│  Model  │◀──│ Model │◀──│  File   │
└─────────┘   └───────┘   └─────────┘
```

Because the Child model was only updated by the Model creators when a stable version was working, the people working on the scene file could synchronize their layer references any time without having to worry about bringing in an incomplete model that was still being worked on.

For an introduction to layer references and the complex situations they solve, see [How To Use Reference Layers - Anime Studio Pro 11 Tutorial](https://www.youtube.com/watch?v=TfkYIZyFYoU&feature=emb_title).

### Project Structure

Cartoon Saloon employed a number of techniques to ensure their project was organized well.

In a layout/scene file, the following layer groups exist

- CHARACTERS
- BACKGROUNDS
- PROPS
- ANIMATIC

The animatic video is rendered in one of the corners while working. Audio is brought in as a separate track.

Shadows are kept outside characters as they are heavily context dependant and could disappear or change shape based on where the character is and what is happening. They are rendered as their own compositing group.

Effects and background animation were done last as a separate pass and then composited together.

The rendered area was 10% larger on all sides than the actual video to be made to allow the composite team to adjust the camera last-minute, if needed. Render safety marks were on another layer which showed the bounding area.

#### Colors and Styles

Colors were created using Moho styles. Character model themes were applied by executing scripts that changed the colors used by the styles.

#### Timeline and Color Coding

Moho doesn't show all timeline channels by default. This can be configured via `View -> Timeline Channels`.

Once all of the timeline channels are shown (or the ones of your choosing), Moho will show separate channels for each bone color.

The bone colors are shown as channels in the timeline in the following order, top to bottom:

- Coral
- Cadet Blue
- Turquoise
- Pink
- Tan
- Purple
- Blue
- Green
- Yellow
- Orange
- Red

Cartoon Saloon exploited this color ordering to make their rigs easier to use in the timeline. Animators were expected to make broad movements first (body turn, head position, etc), and so these bones were colored with colors that appear before the other colors in the timeline. As the timeline was scrolled down, it revealed bone colors that corresponded to more fine-grain actions, such as eye blinks and mouth positions.

#### Units and Scaling

The main character can be made 1.0 Moho units tall so that everything else can be scaled to it appropriately.

#### Rigging

Bone layers can be placed inside switch layers, which allows for switching between multiple rigged versions of something. This was done by Cartoon Saloon on their character's wings as they had multiple different rigged wings (drawn in various angles) to select between.

