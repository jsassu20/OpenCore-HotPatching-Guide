# Power Idle Management

## Description

-This patch enables the power idle management of the MAC system itself to extend the standby time in battery working mode.
-See: <https://pikeralpha.wordpress.com/2017/01/12/debugging-sleep-issues/>.

## SSDT Patch

-***SSDT-DeepIdle***-Power Idle Management Patch

## Precautions

-***SSDT-DeepIdle*** and `S3` sleep may have serious conflicts, use ***SSDT-DeepIdle*** to avoid `S3` sleep, please refer to "Prohibit S3 sleep"
-***SSDT-DeepIdle*** may cause difficulty in waking up the machine. This problem can be solved through a patch, see "AOAC Wake Up Patch"

## Remarks

-***SSDT-DeepIdle*** The main content comes from @Pike R.Alpha
