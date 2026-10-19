# How the plan tracer emerged

This is a concept history of plan tracing in the BFW Wegweiser repository, not a selection of files or commits to port. It follows the shift from using plans to show routes, to needing a curated floor-plan artifact for zoning. Prototype behavior is evidence of exploration, not automatically an approved design.

## From route-finding to a simplified plan

The project began with path-finding over a floor-plan image. The first contribution added an image with marked nodes and a program that found paths through it (`0f2eaeb`). On September 8, the work was split into an offline compiler and a browser client (`2d3f8d7`); PR 1 (`a0a6855`) merged that contribution the following day. The plan remained the map on which routes were shown; there was not yet a drawing application.

The project model was evolving too. A September 11 proposal (`0de4f03`) centered semantic floor plans and a portal graph; that framing was soon reconsidered in a model overhaul. On September 17, the East Wing route prototype (`a39e341`) arrived amid this shift and added the earliest committed drawn simplified floor-plan artifact found in the branch history. It is a static semantic map: zones, portals, movement lines, obstacles and labels are drawn as SVG geometry for a route viewer to use. The viewer loads it; it does not create or edit it. This is a semantic model and routing view, not a tracing tool or the later plan SVG.

The later Claude Design prompt explicitly says the East Wing SVG used older IDs and markup and must not be reused. It is a conceptual predecessor, not a source artifact for the full-floor work.

PR 2 was a parallel documentation-restructuring effort, not the source of that drawing. Its system design and implementation plan describe zoning as producing a semantic simplified SVG and connectivity graph together; the plan calls for a schematic produced from source material and then checked by a person. The SVG is still the zoning product, not a separate curated reference plan. PR 2 was squashed as `f3dd43f`; when the main branch was rebuilt, the East Wing work was integrated at its branch point (`73141a3`) and the documentation restructure was replayed as `0ddf03b`.

## Full-floor EG work and Redline

The full-floor EG zoning work came later. Claude Design produced a zone artifact from the Lageplan and emergency-plan photos; the exact start of that work is unknown. Claude Code then reviewed it. The surviving `docs/working-record.md` describes the sources as messy, large, incomplete or contradictory, with information that should not all be carried into the model.

The EG Redline (introduced in `6b52a8d`) gave the zoning author a way to mark requested corrections on the plan for Claude to interpret. It displayed an image and existing zone and portal geometry and saved marks in plan coordinates. The author marked corrections; the assistant interpreted and applied them, checked the model, and regenerated the view for the author to review. That made it a visual communication and review tool, not a general architectural-plan tracer.

The Redline was updated in `e0c4ef0` and `7cc4506`, then moved into the legacy area in `0572246` and `5b1a250`. That history shows its transition from an active review tool to a preserved example.

The proposal in `claude/review/app-plan.md` tried to carry that work into a zoning authoring application (added in `7cc4506`, revised in `e8102c9` and `c326569`, and later removed in `de89828`). In the October 3 discussion, the authoring view was distinguished from the navigation map: two SVG outputs were needed, one to show the zoning result and another to show routes for navigation users. “Simplified floor plan SVG” was an early name; “Zoned SVG” was provisionally used for the semantic zoning product. During decluttering, the cloud scripts were set aside as inspiration rather than implementation to port, and the standalone-app plan was abandoned in favor of a zoning module and editor: the scripts were difficult to adapt locally, and the standalone app no longer had a place in the revised architecture. This was a change in direction, not yet the plan-tracer design.

## The EG prototype exposes the missing artifact

A separate `feature/eg-prototype` branch made the missing link concrete. Its process record says the matching floor plan was not available as a standalone SVG: the Redline page held both an embedded JPEG underlay and polygon and portal geometry. A PDF renderer was unavailable in that workspace, so the embedded Redline data was the faster source for the navigation demonstration.

The branch's SVG-building script (`adda01b`) extracted the Redline image and geometry, compared 193 zone IDs and 219 portal IDs with the routing graph, and checked portal midpoint alignment. It then wrote a display SVG containing the raster image underlay together with the shell, zones, labels and portals. Its tests confirm that the image remains embedded. The process record explicitly says this demonstrates data compatibility, not that the geometry is authoritative or approved; the output is not the finalized Navigation SVG.

So the prototype could display routes over the EG model, but it still did not leave behind a separate, curated architectural plan. The source image and zoning geometry were bundled into one display artifact. The route viewer and later movement-network changes on this branch refined the demonstration; they did not add a tool for producing a clean source-plan drawing.

## Why the plan tracer became a separate stage

The October 9 artifact chain resolves that gap: **source material → plan SVG → zone SVG → navigation SVG**. It is a supply chain, not a derivation: each stage has its own authored or generated artifact and role; the zone SVG is not simply a transformation of the plan SVG. The plan tracer takes source images as underlays and lets the author align them and manually draw the useful plan information. Its output, the plan SVG, is then used as the zoning editor's reference underlay. The later design also makes the plan tracer the source of the floor id, level, frame and scale, which the zoning editor takes from the plan SVG. The source material can be messy or disagree; the curated plan retains what is relevant without carrying the zoning model or every detail in the originals. Automation was explicitly left for later.

This also explains the changing names. “Simplified floor plan SVG” and “Zoned SVG” described an earlier semantic floor-plan product; “zone SVG” later became the name for the zoning editor's output. “Plan SVG” names the separate upstream drawing. The East Wing SVG belongs to the earlier semantic-map lineage; the EG prototype's display SVG is a composite with an embedded raster; neither is the later plan SVG.

The actual plan tracer code arrives only on `feature/plan-tracer`, created from `local/simpler` in October. Its first implementation (`9550f28`) was a small single-page tool with one image underlay and basic line and text drawing. It was then replaced by a larger version (`e1452a9`) and expanded over subsequent revisions; a test suite followed (`e34f1a1`). The code is much later than the concept lineage and is not an evolution of the East Wing route viewer.

The current plan-tracer specification records the intended manual drawing workflow, while its readme still describes the tool as generating the plan SVG. The contemporary notes flag that readme as not yet reconciled. This mismatch is a reminder to treat current specifications as evidence of intended direction and older implementation descriptions as historical evidence, not as equally authoritative accounts.

## Other branches and what they establish

The relevant work ran through `feature/east-wing-prototype`, `feature/eg-prototype`, `local/simpler` (the simplification branch) and `feature/plan-tracer`. The main and model-overhaul branches share the same head; the SQL branch adds no separate plan-tracing history; the tracker branch records a reconstruction of a proposed third pull request, not an actual pull request. PR 1 and PR 2 are the only actual pull requests.

## What remains uncertain

The repository does not establish exactly how the East Wing SVG was drawn, when the Claude Design work began, or whether any code was directly reused from Redline in the plan tracer. The evidence does establish the conceptual progression: an early static semantic map, a full-floor zoning and review workflow whose plan was embedded with its geometry, and then a separate manual stage for making a curated plan SVG.
