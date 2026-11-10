# DESIGN.md

DESIGN.md is the file at the project root that records the project's visual system: token primitives in frontmatter (colors, typography, radii, spacing, components) and the decisions behind them in prose. It follows the external design.md format that Stitch's linter validates, so it carries no Impeccable schema stamp. `/impeccable document` generates it from existing code, together with a sidecar at `.impeccable/design.json` for what that format cannot hold, such as motion, shadow and breakpoint tokens.

Not to be confused with [PRODUCT.md](product-md.md), which holds product truth. A missing DESIGN.md does not make a project new: the agent still reads the visual identity the code already has.

See [Project files and git](../client/project-files-and-git.md). The generation steps are in [document.md](../../skill/reference/document.md).
