# Sometimes it's a README fix, or something like that - which isn't relevant for
# including in a project's CHANGELOG for example
declared_trivial = github.pr_title.include? "#trivial"

# Make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn("PR is classed as Work in Progress") if github.pr_title.include? "[WIP]"

# Warn when there is a big PR
warn("Big PR") if git.lines_of_code > 500

# Don't let testing shortcuts get into main by accident
fail("fdescribe left in tests") if `grep -r fdescribe specs/ `.length > 1
fail("fit left in tests") if `grep -r fit specs/ `.length > 1

# Danger上でSwiftLintを動かしGitHubにコメントするコード
swiftlint.config_file = '.swiftlint.yml'
swiftlint.binary_path = 'Pods/SwiftLint/swiftlint'
swiftlint.lint_all_files = false
swiftlint.lint_files

# GitHubのPRが形式通りに記述されているかをレビューするコード
is_to_main = github.branch_for_base == 'main'
is_to_develop = github.branch_for_base == 'develop'
is_written_what_is_this_pr_do_title = github.pr_body.match(/^(?=.*What's this PR do \?)/)
is_written_what_is_this_pr_do_example = github.pr_body.match(/概要は必ず記入してください。/)
is_written_how_to_check_title = github.pr_body.match(/^(?=.*How to check)/)
is_written_how_to_check_example = github.pr_body.match(/チェック項目は必ず記入してください。/)
is_written_related_ticket_title = github.pr_body.match(/^(?=.*Related Tickets)/)
has_danger_changed = git.diff_for_file("Dangerfile")
has_podfile_changed = git.diff_for_file("Podfile")
has_podfile_lock_changed = git.diff_for_file("Podfile.lock")
is_set_ticket_url = github.pr_body.match(/hogehoge.backlog.jp\/view\/(Hogehoge_IPHONE_APP|Hoge_TEAM|Hoge_PRDCT)-[0-9]{1,}/)
is_set_ticket_title = !!github.pr_title.match(/(Hogehoge_IPHONE_APP|Hoge_TEAM|Hoge_PRDCT)-[0-9]{1,}/)
is_from_release_main = !!github.branch_for_head.match(/release\/([A-Z]{,3}_)*v[0-9]+\.[0-9][0-9]+\.[0-9]\/main/)
is_from_hotfix_main = !!github.branch_for_head.match(/hotfix\/main/)
is_from_project_main = !!github.branch_for_head.match(/feature\/.*\/main/)
is_from_feature = !!github.branch_for_head.match(/feature\/(Hogehoge_IPHONE_APP|Hoge_TEAM|Hoge_PRDCT)-[0-9]{1,}/)
is_from_main = !!github.branch_for_head.match(/main/)

# マージ先ブランチが正しくなかった時にエラー
if is_to_main && (!is_from_release_main || !is_from_hotfix_main)
    fail("以下のブランチ以外からはmainへマージできません。
            - `release/xxxx/main`
            - `hotfix/main`")
elsif is_to_develop && (!is_from_release_main || !is_from_project_main || !is_from_feature || !is_from_main)
    fail("以下のブランチ以外からはdevelopへマージできません。
            - `release/xxxx/main`
            - `feature/xxx/main`
            - `feature/チケット番号`
            - `main`")
end

# コードの変更量が多かった時に警告
warn("PRの変更量が多すぎます。
    可能であればPRを分割してください。") if git.lines_of_code > 500

# ラベルが設定されていなかった時に警告
warn("このPRにはラベルが設定されていません。
    ラベルを1つ以上付けてください。") if github.pr_labels.empty?

# Dangerfileが変更された時に警告
if has_danger_changed
    warn("`Dangerfile`が変更されました。
    変更内容を確認してください。")
end

# Podfileが変更された時に警告
if has_podfile_changed || has_podfile_lock_changed
    warn("`Podfile`もしくは`Podfile.lock`が変更されました。
        変更内容を確認してください。") 
end

# PRボディにチケットが登録されていなければ警告
warn "担当したチケットURLをPRに記載してください。" unless is_set_ticket_url

# Related Ticketが記載されていなければ警告
warn("`Related Tickets`が未記入です。
    チケット情報を記載してください。") unless is_written_related_ticket_title

# How to checkがデフォルトのまま or そもそもなかったら警告
if !is_written_how_to_check_title || is_written_how_to_check_example
    warn("`How to check`が未記入もしくは`Check List`に例文が記入されているままです。
    動作確認手順をPRに記載してください。")
end

# what is this pr doがデフォルトのまま or そもそもなかったらエラー
if !is_written_what_is_this_pr_do_title || is_written_what_is_this_pr_do_example
    fail("`What's this PR do ?`が未記入もしくは例文が記入されているままです。
        何を行ったかをPRに記載してください。")
end

# PRタイトルをチェックし、何もチケットが含まれていなかったら警告
warn "PRタイトルにはチケットタイトルを含めてください。" unless is_set_ticket_title
