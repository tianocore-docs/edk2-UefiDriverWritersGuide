<!--- @file
  24.2.2 ReadBlocks() and ReadBlocksEx() Implementation

  Copyright (c) 2012-2018, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->

### 24.2.2 ReadBlocks() and ReadBlocksEx() Implementation

Reading blocks from media typically uses the following order of operations:

1. Verify media presence. This is critical for removable or swappable media.

2. If a media change event is detected, then reinstall the Block I/O Protocols
   using the UEFI Boot Service `ReinstallProtocolInterface()`. A media change
   event can be a change from the media present state to the media not present
   state. A change from the media not present state to the media present state.
   The _BlockSize_ field of the _Media_ structure must have a nonzero value,
   even when no media is present.

    * If there is no media, return EFI_NO_MEDIA.

    * If the media is different, return EFI_MEDIA_CHANGED.

3. Verify parameters

    * The _Buffer_, sized _BufferSize_, must be a whole number of blocks

    * The read does not start past the end of the media

    * The read does not extend past the end of the media

    * The _Buffer_ is aligned as required

4. Read the requested sectors from the media

    * If a non-blocking request is made through ReadBlocksEx(), then start the
      request and if the request is expected to take some time to complete, set
      up a timer event to periodically check the completion status and return
      immediately. When the request is complete, signal the event passed into
      ReadBlocksEx() to inform the caller that the previous request has been
      completed.

5. If needed, copy the appropriate portion of the read into _Buffer_.

    * (Optional) Update the driver's cache for better performance.
