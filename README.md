# bai5
using System;
using System.Drawing;
using System.Drawing.Text;
using System.IO;
using System.Windows.Forms;

namespace Lab03_2
{
    public partial class Form1 : Form
    {
        private string currentFilePath = ""; // Lưu đường dẫn file hiện tại

        public Form1()
        {
            InitializeComponent();
        }

        // ======== SỰ KIỆN LOAD FORM ========
        private void Form1_Load(object sender, EventArgs e)
        {
            LoadFontList();
            LoadFontSize();
            rtbVanBan.Font = new Font("Tahoma", 14, FontStyle.Regular);
        }

        // ======== NẠP DANH SÁCH FONT ========
        private void LoadFontList()
        {
            cbxFont.Items.Clear();
            foreach (FontFamily fontFamily in new InstalledFontCollection().Families)
            {
                cbxFont.Items.Add(fontFamily.Name);
            }
            cbxFont.SelectedItem = "Tahoma";
        }

        // ======== NẠP DANH SÁCH CỠ CHỮ ========
        private void LoadFontSize()
        {
            int[] sizes = { 8, 9, 10, 11, 12, 14, 16, 18, 20, 22, 24, 26, 28, 36, 48, 72 };
            cbxSize.ComboBox.DataSource = sizes;
            cbxSize.SelectedItem = 14;
        }

        // ======== TẠO VĂN BẢN MỚI ========
        private void toolStripButton1_Click(object sender, EventArgs e)
        {
            rtbVanBan.Clear();
            currentFilePath = "";

            cbxFont.SelectedItem = "Tahoma";
            cbxSize.SelectedItem = 14;
            rtbVanBan.Font = new Font("Tahoma", 14, FontStyle.Regular);

            MessageBox.Show("Đã tạo văn bản mới!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }

        // ======== MỞ TỆP ========
        private void openToolStripMenuItem_Click(object sender, EventArgs e)
        {
            OpenFileDialog dlg = new OpenFileDialog();
            dlg.Filter = "Rich Text Format (*.rtf)|*.rtf|Text Files (*.txt)|*.txt";
            dlg.Multiselect = false;

            if (dlg.ShowDialog() == DialogResult.OK)
            {
                try
                {
                    string path = dlg.FileName;
                    if (Path.GetExtension(path).ToLower() == ".txt")
                        rtbVanBan.LoadFile(path, RichTextBoxStreamType.PlainText);
                    else
                        rtbVanBan.LoadFile(path, RichTextBoxStreamType.RichText);

                    currentFilePath = path;
                    MessageBox.Show("Đã mở tệp tin thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Lỗi khi mở tệp tin: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
                }
            }
        }

        // ======== LƯU TỆP ========
        private void toolStripButton2_Click(object sender, EventArgs e)
        {
            try
            {
                if (string.IsNullOrEmpty(currentFilePath))
                {
                    SaveFileDialog dlg = new SaveFileDialog();
                    dlg.Filter = "Rich Text Format (*.rtf)|*.rtf";
                    dlg.Title = "Lưu văn bản";

                    if (dlg.ShowDialog() == DialogResult.OK)
                    {
                        currentFilePath = dlg.FileName;
                        rtbVanBan.SaveFile(currentFilePath, RichTextBoxStreamType.RichText);
                    }
                }
                else
                {
                    rtbVanBan.SaveFile(currentFilePath, RichTextBoxStreamType.RichText);
                }

                MessageBox.Show("Lưu văn bản thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            catch (Exception ex)
            {
                MessageBox.Show("Lỗi khi lưu tệp tin: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        // ======== ĐỊNH DẠNG (FontDialog) ========
        private void địnhDạngToolStripMenuItem_Click(object sender, EventArgs e)
        {
            FontDialog fontDlg = new FontDialog
            {
                ShowColor = true,
                ShowApply = true,
                ShowHelp = true,
                Font = rtbVanBan.SelectionFont ?? rtbVanBan.Font,
                Color = rtbVanBan.SelectionColor
            };

            if (fontDlg.ShowDialog() == DialogResult.OK)
            {
                rtbVanBan.SelectionFont = fontDlg.Font;
                rtbVanBan.SelectionColor = fontDlg.Color;
            }
        }

        // ======== NÚT IN ĐẬM ========
        private void toolStripButtonBold_Click(object sender, EventArgs e)
        {
            if (rtbVanBan.SelectionFont != null)
            {
                FontStyle style = rtbVanBan.SelectionFont.Bold
                    ? rtbVanBan.SelectionFont.Style & ~FontStyle.Bold
                    : rtbVanBan.SelectionFont.Style | FontStyle.Bold;

                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        // ======== NÚT IN NGHIÊNG ========
        private void toolStripButtonItalic_Click(object sender, EventArgs e)
        {
            if (rtbVanBan.SelectionFont != null)
            {
                FontStyle style = rtbVanBan.SelectionFont.Italic
                    ? rtbVanBan.SelectionFont.Style & ~FontStyle.Italic
                    : rtbVanBan.SelectionFont.Style | FontStyle.Italic;

                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        // ======== NÚT GẠCH DƯỚI ========
        private void toolStripButtonUnderline_Click(object sender, EventArgs e)
        {
            if (rtbVanBan.SelectionFont != null)
            {
                FontStyle style = rtbVanBan.SelectionFont.Underline
                    ? rtbVanBan.SelectionFont.Style & ~FontStyle.Underline
                    : rtbVanBan.SelectionFont.Style | FontStyle.Underline;

                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        // ======== KHI CHỌN FONT TRONG COMBOBOX ========
        private void cbxFont_SelectedIndexChanged(object sender, EventArgs e)
        {
            if (cbxFont.SelectedItem != null && rtbVanBan.SelectionFont != null)
            {
                string selectedFont = cbxFont.SelectedItem.ToString();
                float currentSize = rtbVanBan.SelectionFont.Size;
                FontStyle style = rtbVanBan.SelectionFont.Style;
                rtbVanBan.SelectionFont = new Font(selectedFont, currentSize, style);
            }
        }

        // ======== KHI CHỌN SIZE TRONG COMBOBOX ========
        private void cbxSize_SelectedIndexChanged(object sender, EventArgs e)
        {
            if (cbxSize.SelectedItem != null && rtbVanBan.SelectionFont != null)
            {
                float newSize = Convert.ToSingle(cbxSize.SelectedItem);
                string currentFont = rtbVanBan.SelectionFont.FontFamily.Name;
                FontStyle style = rtbVanBan.SelectionFont.Style;
                rtbVanBan.SelectionFont = new Font(currentFont, newSize, style);
            }
        }

        private void btnBold_Click(object sender, EventArgs e)
        {
            // Kiểm tra xem có đoạn văn bản nào được chọn không
            if (rtbVanBan.SelectionFont != null)
            {
                // Lấy kiểu font hiện tại của đoạn văn bản được chọn
                FontStyle style = rtbVanBan.SelectionFont.Style;

                // Nếu đang đậm -> bỏ đậm, ngược lại -> thêm đậm
                if (rtbVanBan.SelectionFont.Bold)
                    style &= ~FontStyle.Bold; // Bỏ Bold
                else
                    style |= FontStyle.Bold;  // Thêm Bold

                // Cập nhật lại font cho đoạn văn bản được chọn
                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        private void btnItalic_Click(object sender, EventArgs e)
        {
            // Kiểm tra xem có đoạn văn bản nào đang được chọn không
            if (rtbVanBan.SelectionFont != null)
            {
                // Lấy kiểu font hiện tại của đoạn văn bản
                FontStyle style = rtbVanBan.SelectionFont.Style;

                // Nếu đã nghiêng thì bỏ nghiêng, ngược lại thì thêm nghiêng
                if (rtbVanBan.SelectionFont.Italic)
                    style &= ~FontStyle.Italic;   // Bỏ Italic
                else
                    style |= FontStyle.Italic;    // Thêm Italic

                // Cập nhật lại font cho đoạn văn bản được chọn
                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        private void btnUnderline_Click(object sender, EventArgs e)
        {
            // Kiểm tra xem có đoạn văn bản nào được chọn không
            if (rtbVanBan.SelectionFont != null)
            {
                // Lấy kiểu font hiện tại
                FontStyle style = rtbVanBan.SelectionFont.Style;

                // Nếu đang có gạch dưới -> bỏ đi, ngược lại -> thêm vào
                if (rtbVanBan.SelectionFont.Underline)
                    style &= ~FontStyle.Underline;   // Bỏ Underline
                else
                    style |= FontStyle.Underline;    // Thêm Underline

                // Cập nhật lại font cho đoạn văn bản được chọn
                rtbVanBan.SelectionFont = new Font(rtbVanBan.SelectionFont, style);
            }
        }

        private void cbxFont_Click(object sender, EventArgs e)
        {
            try
            {
                if (cbxFont.SelectedItem != null)
                {
                    string selectedFont = cbxFont.SelectedItem.ToString();
                    float currentSize = rtbVanBan.SelectionFont?.Size ?? 14;

                    // Giữ nguyên các định dạng (Bold, Italic, Underline) hiện tại
                    FontStyle currentStyle = rtbVanBan.SelectionFont?.Style ?? FontStyle.Regular;

                    rtbVanBan.SelectionFont = new Font(selectedFont, currentSize, currentStyle);
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Không thể thay đổi font: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private void cbxSize_Click(object sender, EventArgs e)
        {
            try
            {
                if (cbxSize.Items.Count == 0)
                {
                    // Danh sách cỡ chữ phổ biến
                    int[] sizeValues = new int[]
                    {
                8, 9, 10, 11, 12, 14, 16, 18, 20, 22,
                24, 26, 28, 36, 48, 72
                    };

                    foreach (int size in sizeValues)
                    {
                        cbxSize.Items.Add(size);
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Lỗi khi tải danh sách cỡ chữ: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }
    }
}
