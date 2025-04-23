- ~/.emacs.d/init.el

```elisp
(setq user-mail-address "adam@schaefers.dev" ;; FROM
      user-full-name "sudoshred")

(load-file "~/.authinfo.irc.el.gpg")

(defun send-buffer-to-termbin ()
  "Send the entire buffer to termbin.com:9999 and copy the resulting URL to the clipboard."
  (interactive)
  (let* ((temp-file (make-temp-file "termbin-buffer-"))
         (nc-command (format "cat %s | nc termbin.com 9999" temp-file))
         (output-buffer "*termbin-output*"))
    (write-region (point-min) (point-max) temp-file nil 'silent)
    (with-current-buffer (get-buffer-create output-buffer)
      (erase-buffer)
      (let ((exit-code (call-process-shell-command nc-command nil t)))
        (if (zerop exit-code)
            (let ((url (string-trim (buffer-string))))
              (kill-new url)
              (message "Buffer sent to termbin: %s (copied to clipboard)" url))
          (message "Failed to send buffer to termbin"))))
    (delete-file temp-file)))

(global-set-key (kbd "C-c t") 'send-buffer-to-termbin)

(load-file "~/.authinfo.irc.el.gpg")

(require 'rcirc)

(setopt rcirc-default-nick "aesdef"
        ;; rcirc-default-user-name "aesdef"
        ;; rcirc-default-full-name "aesdef"
        )

(setopt rcirc-authinfo (list (list "irc.libera.chat" 'nickserv "aesdef" my-irc-passwd)))


(setq rcirc-server-alist
      '(
	;; ("archie-serve"
        ;;  :port "9999"
        ;;  :channels ("#Main")
        ;;  ;; :nick "aesdef"
        ;;  )
        ("irc.libera.chat" :channels
         (
          "#emacs"
          "#archlinux"
          )
         :port 6697
         :encryption tls
         ;; :nick "aesdef"
         )
        ))

```

- ~/.gnus.el

```elisp
(setq gnus-select-method '(nnml ""))

;; incoming
(add-to-list 'mail-sources
             `(pop :server "mail.riseup.net"
                   :port 995
                   :user , "YOUR_POP_USERNAME_HERE" ;; INBOX
                   :stream ssl))

;; outgoing
(setq user-mail-address "adam@schaefers.dev"
      user-full-name "Adam Schaefers"
      message-send-mail-function 'smtpmail-send-it
      smtpmail-default-smtp-server "email-smtp.us-east-1.amazonaws.com"
      smtpmail-smtp-server "email-smtp.us-east-1.amazonaws.com"
      smtpmail-smtp-service 587
      smtpmail-smtp-user "YOUR_SMTP_USERNAME_HERE"
      smtpmail-stream-type 'starttls
      smtpmail-auth-credentials "~/.authinfo.gpg"
      smtpmail-debug-info t)

```

- ~/.authinfo.gpg

```
machine mail.riseup.net login YOUR_POP_USERNAME_HERE password YOUR_POP_PASSWORD_HERE port 995
machine email-smtp.us-east-1.amazonaws.com login YOUR_SMTP_USERNAME_HERE password YOUR_SMTP_PASSWORD_HERE port 587
```

- ~/.authinfo.irc.el.gpg

```
(setq-default my-irc-passwd "YOUR_IRC_PASSWORD_HERE")
```
