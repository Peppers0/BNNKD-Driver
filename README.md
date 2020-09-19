# BNNKD-Driver
Simple driver that uses IOCTL communication method

Use with KDMapper to manually map your driver using a vulnerable Intel Driver

Note: Change areas where comments say "you must change this" to mask your driver

Example of getting Process ID and base address of a process:
```
const HANDLE driver = CreateFile(
    L"\\\\.\\NsiLookup",
    GENERIC_ALL,
    FILE_SHARE_READ | FILE_SHARE_WRITE,
    nullptr,
    OPEN_EXISTING,
    FILE_ATTRIBUTE_NORMAL,
    nullptr
);

if (driver == INVALID_HANDLE_VALUE) {
    MessageBox(NULL, L"Can't open", L"Error", 0);
    return GetLastError();
}

const wchar_t* n = L"explorer.exe";

PidBaseResponse result = { 0 };
PidBase cmd2 = { 0 };
memcpy(cmd2.name, n, wcslen(n) * sizeof(wchar_t));
cmd2.RetInfoPid = (uintptr_t)&result.pid;
cmd2.RetInfoAddr = (uintptr_t)&result.baseAddr;

if (!DeviceIoControl(driver, GET_PID_AND_BASE, &cmd2, sizeof(PidBase), NULL, NULL, NULL, NULL)) {
    MessageBox(NULL, L"Unknown", L"Error", 0);
    return GetLastError();
}

if (result.pid == 0) {
    MessageBox(NULL, L"No info received", L"Error", 0);
    return FALSE;
}
```
