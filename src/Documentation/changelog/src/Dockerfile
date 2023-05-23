FROM orhunp/git-cliff:1.2.0

LABEL maintainer="jimbrig <jimmy.briggs@jimbrig.com>"
LABEL repository="https://github.com/jimbrig/ghactions"
LABEL homepage="https://github.com/orhun/git-cliff"

LABEL com.github.actions.name="Changelog Generator"
LABEL com.github.actions.description="Generate changelog based on your Git history"
LABEL com.github.actions.icon="triangle"
LABEL com.github.actions.color="green"

COPY README.md /
COPY cliff.toml /
COPY entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]
