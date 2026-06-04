Firstly I'm quite sure this is the janky way of setting this up, but was what I was able to come up with in 4 hours, but it's easy to copy & paste. 

    • Note: Sys Swap shows the total amount of swap your system has. If you have Zram configured as swap, Sys Swap shows there being 8GiB of swap.



Step T: Open your neowofetch config file.
    • nano ~.config/neowofetch/config.conf

Step 0: Copy one of the listed below configs into the print_info() { info clos}
section of the config file.

    • Note: config-T adds iB/B to outputs that output G/Gi, config-0 is the defualt output of those outputs,
    config-1, & config-1T are an alt of T & 0 that print null if zram isn't installed or configured.
      
    • Note: I genrally would put custom config part below info "Memory" memory.

Config-T:

    prin "Zram" "$(zramctl | awk 'NR==2 {printf "%siB, %s, comp=%s, total=%s, %s, %s, Using %s %s", $3,$4,$5,$6,$1,$7,$2,$8}')"
    prin "Zswap" "$(
    printf 'enabled=%s shrinker=%s comp=%s accept=%s%% max=%s%%' \
        "$(cat /sys/module/zswap/parameters/enabled)" \
        "$(cat /sys/module/zswap/parameters/shrinker_enabled)" \
        "$(cat /sys/module/zswap/parameters/compressor)" \
        "$(cat /sys/module/zswap/parameters/accept_threshold_percent)" \
        "$(cat /sys/module/zswap/parameters/max_pool_percent)"
    )"
    prin "Sys Swap" "$(free -h | awk '/Swap/ {print $3 " used of " $2}')"B


Config-0:

    prin "Zram" "$(zramctl | awk 'NR==2 {printf "%s, %s, comp=%s, total=%s, %s, %s, Using %s %s", $3,$4,$5,$6,$1,$7,$2,$8}')"
    prin "Zswap" "$(
    printf 'enabled=%s shrinker=%s comp=%s accept=%s%% max=%s%%' \
        "$(cat /sys/module/zswap/parameters/enabled)" \
        "$(cat /sys/module/zswap/parameters/shrinker_enabled)" \
        "$(cat /sys/module/zswap/parameters/compressor)" \
        "$(cat /sys/module/zswap/parameters/accept_threshold_percent)" \
        "$(cat /sys/module/zswap/parameters/max_pool_percent)"
    )"
    prin "Sys Swap" "$(free -h | awk '/Swap/ {print $3 " used of " $2}')"

Config-1

    prin "Zram" "$(
    if command -v zramctl >/dev/null 2>&1; then
        zramctl | awk '
            NR==2 {
                printf "%siB, %s, comp=%s, total=%s, %s, %s, Using %s %s",
                       $3,$4,$5,$6,$1,$7,$2,$8
                found=1
            }
            END {
                if (!found)
                    print "Null"
            }
        '
    else
        echo "Null"
    fi
    )"
    prin "Zswap" "$(
    printf 'enabled=%s shrinker=%s comp=%s accept=%s%% max=%s%%' \
        "$(cat /sys/module/zswap/parameters/enabled)" \
        "$(cat /sys/module/zswap/parameters/shrinker_enabled)" \
        "$(cat /sys/module/zswap/parameters/compressor)" \
        "$(cat /sys/module/zswap/parameters/accept_threshold_percent)" \
        "$(cat /sys/module/zswap/parameters/max_pool_percent)"
    )"
    prin "Sys Swap" "$(free -h | awk '/Swap/ {print $3 " used of " $2}')"B

Config-1T

    prin "Zram" "$(
    if command -v zramctl >/dev/null 2>&1; then
        zramctl | awk '
            NR==2 {
                printf "%s, %s, comp=%s, total=%s, %s, %s, Using %s %s",
                       $3,$4,$5,$6,$1,$7,$2,$8
                found=1
            }
            END {
                if (!found)
                    print "Null"
            }
        '
    else
        echo "Null"
    fi
    )"
    prin "Zswap" "$(
    printf 'enabled=%s shrinker=%s comp=%s accept=%s%% max=%s%%' \
        "$(cat /sys/module/zswap/parameters/enabled)" \
        "$(cat /sys/module/zswap/parameters/shrinker_enabled)" \
        "$(cat /sys/module/zswap/parameters/compressor)" \
        "$(cat /sys/module/zswap/parameters/accept_threshold_percent)" \
        "$(cat /sys/module/zswap/parameters/max_pool_percent)"
    )"
    prin "Sys Swap" "$(free -h | awk '/Swap/ {print $3 " used of " $2}')"

Step 1: Save & exit, & run hyfetch.
