# BOS_Pl```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace BOS_Platform.Domain.Entities
{
    // THE COMPANIES TABLE (Root Enterprise Corporate Identity)
    [Table("Companies")]
    public class Company
    {
        [Key]
        public Guid CompanyID { get; set; } = Guid.NewGuid();

        [Required]
        [StringLength(150)]
        public string GlobalTenantName { get; set; } = string.Empty;

        [Required]
        [StringLength(3)] // ISO Alpha-3 Country Code (e.g., "ZAF", "GBR")
        public string HeadquartersCountry { get; set; } = string.Empty;

        public DateTime CreatedAtUtc { get; set; } = DateTime.UtcNow;

        // Navigation Property: A single global company contains multiple branches
        public virtual ICollection<Branch> Branches { get; set; } = new List<Branch>();
    }
}
```atform
```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace BOS_Platform.Domain.Entities
{
    // THE BRANCHES TABLE (Regional Data Ring-Fencing & Compliance)
    [Table("Branches")]
    public class Branch
    {
        [Key]
        public Guid BranchID { get; set; } = Guid.NewGuid();

        [Required]
        public Guid CompanyID { get; set; }

        [ForeignKey(nameof(CompanyID))]
        public virtual Company Company { get; set; } = null!;

        [Required]
        [StringLength(100)]
        public string BranchName { get; set; } = string.Empty;

        [Required]
        [StringLength(5)] // Regional indicators (e.g., "UK", "ZA-GP")
        public string RegionCode { get; set; } = string.Empty;

        [Required]
        [StringLength(50)] // Explicitly identifies compliance zones (e.g., "GDPR", "POPIA")
        public string DataSovereigntyZone { get; set; } = string.Empty;

        // Navigation Properties
        public virtual ICollection<User> Users { get; set; } = new List<User>();
        public virtual ICollection<DocumentMetadata> Documents { get; set; } = new List<DocumentMetadata>();
    }
}
```
```csharp
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace BOS_Platform.Domain.Entities
{
    // THE USERS TABLE (Identity & Security Access Controls)
    [Table("Users")]
    public class User
    {
        [Key]
        public Guid UserID { get; set; } = Guid.NewGuid();

        [Required]
        public Guid BranchID { get; set; }

        [ForeignKey(nameof(BranchID))]
        public virtual Branch Branch { get; set; } = null!;

        [Required]
        [StringLength(100)]
        public string FullName { get; set; } = string.Empty;

        [Required]
        [EmailAddress]
        [StringLength(256)]
        public string CorporateEmail { get; set; } = string.Empty;

        [Required]
        [StringLength(50)] // Rigid system roles (e.g., "GlobalAdmin", "BranchManager")
        public string AssignedRole { get; set; } = string.Empty;

        public bool IsActive { get; set; } = true;
    }
}
```
csharp
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace BOS_Platform.Domain.Entities
{
    // THE DOCUMENT METADATA TABLE (The Virtual File Mapping Matrix)
    [Table("DocumentMetadata")]
    public class DocumentMetadata
    {
        [Key]
        public Guid DocID { get; set; } = Guid.NewGuid();

        [Required]
        public Guid BranchID { get; set; }

        [ForeignKey(nameof(BranchID))]
        public virtual Branch Branch { get; set; } = null!;

        [Required]
        [StringLength(255)]
        public string VirtualFileName { get; set; } = string.Empty;

        [Required]
        [Url] // Points to external cloud storage hubs (SharePoint or Google Drive links)
        public string CloudSourceURL { get; set; } = string.Empty;

        [Required]
        [StringLength(30)] // (e.g., "TopSecret", "Secret", "Internal", "Public")
        public string SecurityClassificationLevel { get; set; } = "Internal";

        public long FileSizeBytes { get; set; }

        [Required]
        [StringLength(10)] // Document extension type (e.g., ".pdf", ".docx")
        public string FileExtension { get; set; } = string.Empty;

        public DateTime SynchronizedAtUtc { get; set; } = DateTime.UtcNow;
    }
}
