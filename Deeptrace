local M = {}

local inspect = require 'inspect'

local getlocal, getinfo, getupvalue = debug.getlocal, debug.getinfo, debug.getupvalue
local stderr = io.stderr

function M.handler(...)
    io.stderr:write(inspect(M.collectinfo(3)))
    return ...
end

function M.collectinfo(stackindex)
    local levels = {}
    local stackindex = stackindex or 2
    local info
    repeat
        info = getinfo(stackindex)
        if info then
            local function localhelper(index,direction,tbl)
                local k, v
                repeat
                    k, v = getlocal(stackindex,index)
                    if k~=nil then
                        tbl[index*direction]={name=k,value=v}
                    end
                    index = index + direction
                until k==nil
            end

            local varargs = {}
            local locals = {}

            localhelper(1,1,locals)
            localhelper(-1,-1,varargs)

            local upvalues = {}

            do
                local upindex = 1
                local k, v
                repeat
                    k, v = getupvalue(info.func, upindex)
                    if k~=nil then
                        upvalues[upindex] = {name=k, value=v}
                    end
                    upindex=upindex+1
                until k==nil
            end
            
            levels[stackindex]={
                info=info,
                varargs=varargs,
                locals=locals,
                upvalues=upvalues,
            }
        end
        stackindex = stackindex + 1
    until info == nil
    return levels
end

return M
