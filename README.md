

import re

# Precompiled patterns — compiled once at import time, reused on every call
_RE_SWG_PROXY = re.compile(r'^(?P<entity>[^: ]+)(?::(?P<group>\S+))? automation (?P<description>.+) : (?P<type>\S+)$')
_RE_SSE_PROXY = re.compile(r'^(?P<entity>[^: ]+)(?::(?P<group>\S+))? (?P<description>.+) : (?P<type>\S+)$')
_RE_LST = re.compile(r'^(?P<entity>[^.]+)\.(?P<group>[^.]*)\.(?P<description>[^.]+)\.[^.]+\.(?P<type>[^.]+)\.lst$')


class ListNaming:
    _RE_SWG_PROXY = re.compile(r'^(?P<entity>[^: ]+)(?::(?P<group>\S+))? automation (?P<description>.+) : (?P<type>\S+)$')
_RE_SSE_PROXY = re.compile(r'^(?P<entity>[^: ]+)(?::(?P<group>\S+))? (?P<description>.+) : (?P<type>\S+)$')
_RE_LST = re.compile(r'^(?P<entity>[^.]+)\.(?P<group>[^.]*)\.(?P<description>[^.]+)\.[^.]+\.(?P<type>[^.]+)\.lst$')
    def list_name(self, filename: str, swg: bool = True, sse: bool = False, reverse: bool = False) -> str:
       
        if swg and sse:
            raise ValueError("swg and sse cannot both be True")

        if reverse:
            pattern = _RE_SWG_PROXY if swg else _RE_SSE_PROXY
            m = pattern.match(filename)
            if not m:
                return filename
            entity, group, description, type_ = m.group('entity'), m.group('group') or '', m.group('description'), m.group('type')
            return f"{entity}.{group}.{description}.{description}_{type_}.{type_}.lst"

        if not swg and not sse:
            return filename

        m = _RE_LST.match(filename)
        if not m:
            return filename
        entity, group, description, type_ = m.group('entity'), m.group('group'), m.group('description'), m.group('type')
        keyword = ' automation ' if swg else ' '

        if group:
            return f"{entity}:{group}{keyword}{description} : {type_}"
        return f"{entity}{keyword}{description} : {type_}"
