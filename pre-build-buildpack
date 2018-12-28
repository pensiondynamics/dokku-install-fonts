#!/usr/bin/env bash

set -eo pipefail; [[ $DOKKU_TRACE ]] && set -x

install_fonts() {
  local APP="$1"
  local IMG="dokku/$APP"
  local DIR="/home/dokku/${APP}/install-fonts"

  if [ -d "$DIR" ] ; then
    echo "-----> copying fonts from ${DIR}..."

    while read -rd $'\0' f ; do
      echo "       adding ${f##*/}"

      id=$(docker run -i -a stdin "$IMG" /bin/bash -c "cat > /usr/local/share/fonts/${f##*/}" < "$f")
      test "$(docker wait "$id")" -eq 0
      docker commit "$id" "$IMG" > /dev/null
    done < <(find "$DIR" -type f -print0)

    id=$(docker run -d "$IMG" /bin/bash -e -c "fc-cache")
    test "$(docker wait "$id")" -eq 0
    docker commit "$id" "$IMG" > /dev/null
  else
    echo "-----> install-fonts directory not found"
  fi
}

install_fonts "$@"
